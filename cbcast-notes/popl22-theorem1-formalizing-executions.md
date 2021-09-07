---
header-includes: |
    \usepackage{amssymb}
    \usepackage{bussproofs}
    \newcommand\mdoubleplus{\mathbin{+\mkern-10mu+}}
    \newcommand\hb{\to_{\text{HB}}}
    \newcommand\eZ{e_{\text{z}}}
    \newcommand\eY{e_{\text{y}}}
    \newcommand\eA{e_{\text{a}}}
title: Formalizing and mechanizing executions
...

An attempt to formalize executions.
With these executions we can state the causal safety property, the property of
an execution being guarded by a predicate, and the theorem 1 property from our
POPL22 submission.

# Execution type

An execution is composed of events.

\begin{equation}
\begin{aligned}
    \text{Event} && = && \text{Broadcast}(m)    \\
                 && | && \text{Deliver}_p(m)    \\
    \\
    \text{ProcessState} && = && [\text{Event}]
\end{aligned}
\end{equation}

Events are either broadcast or deliver, and process state is a sequence of
those events.
The order of $\text{ProcessState}$ in this document is such that the head and
tail, written $[x] \mdoubleplus xs$, correspond to the most recent event and
the prior state, but this choice is arbitrary.
The $m$ and $p$ are opaque identifiers which are comparable for equality and
sort order only.

\begin{equation}
\begin{aligned}
    \text{Execution} && = (   && \{\text{Event}\}                       \\
                     &&\times && p \mapsto \text{ProcessState}          \\
                     &&\times && \text{Event} \hb \text{Event}          \\
                     && )                                               \\
    \\
    \text{execution}_0 && = && ( \emptyset \times \emptyset \times \emptyset )
\end{aligned}
\end{equation}

Executions contain a set of events, $E$,
a mapping from process to process state, $M$,
and a happens-before relation on events, $H$.
The initial execution, $\text{execution}_0$, contains no events, maps no
processes to their process state, and has a happens-before relation containing
no pairs.

# Happens-before relation

Let's take a quick digression about the happens-before relation.
To write pairs of events $(e, e')$ in the happens-before relation, in this
document the syntax $e \hb e'$ will be used.
Otherwise, discussion of the happens-before relation will use set-like notation
about the identifier $H$.

The happens-before should be a transitive and irreflexive relation.
We define a function which given a correct happens-before relation $H$, a new
event $\eZ$, and a sequence of events happening immediately prior,
$\eY \in \text{prev}$, produces all the pairs which must be added to the input
happens-before relation.
For example, assuming we have $H$ and a new event $\eZ$ and some existing event
$\eY$, we could produce an updated happens-before relation with $H \cup
\text{newHBpairs}(H,[\eY],\eZ)$.

\begin{equation}
\begin{aligned}
    \text{newHBpairs}   &(H,\text{prev},\eZ)                                \\
                        &= \{ \eY \hb \eZ \quad| \eY \in \text{prev} \}     \\
                        &\cup \{ \eA \hb \eZ \quad| (\eA \hb \eY) \in H
                               \land \eY \in \text{prev} \}
\end{aligned}
\end{equation}

The first clause produces pairs of each event $\eY$ in $\text{prev}$ with the new
event $\eZ$.
The second clause captures the transitive dependencies of $\eZ$ by pairing each
event which happened before each of the $\eY$, called $\eA$, with $\eZ$.
This works because each of the pairs representing the transitive dependencies
of $\eY$ are already included in the relation, and so looking only one level
back to add $\eZ$ is sufficient.
The mnemonic to remember is that $\eA$ is an ancestor of $\eY$ which is
penultimate to $\eZ$ in the partial order expressed by the relation.

# Execution semantics

The transition relation will define what appears in each of the fields of
an execution.

\begin{equation}
\begin{aligned}
\text{Execution} \Rightarrow \text{Execution}
\end{aligned}
\end{equation}

There are two inference rules: broadcast and deliver.

\footnotesize
\begin{prooftree}
    [broadcast]
    \AxiomC{$ e = \text{Broadcast}(m) $}
    \AxiomC{$ e \notin E $}
    \BinaryInfC{$\begin{aligned}
        (E,M,H) \Rightarrow
  (  & E \cup \{e\}
  ,\\& M[p \mapsto ( [e] \mdoubleplus M[p] )]
  ,\\& H \cup \text{newHBpairs}(H,M[p],e)
  \quad )
    \end{aligned}$}
\end{prooftree}
\normalsize

The broadcast rule corresponds to a particular process $p$ broadcasting the
message $m$ to all processes in the system.
The execution is updated in the following ways:

* The new event is added into the set of events $E$.
  * The broadcast event must not already exist in the execution.
* The new event is prepended to the sequence of events for process $p$.
  * The $p$ may be new, without an existing mapping in $M$, in which case
    $M[p]$ would be $[]$ and the result of prepending would be $[e]$.
  * The choice to prepend instead of append is arbitrary.
* The new event has pairs added to $H$ to indicate that it occurs after every
  event $M[p]$ on the same process $p$.
  * The first clause of $\text{newHBpairs}$ returns the new event $\eZ$ paired
    with each event $\eY$ on the same process $p$.
  * The second clause of $\text{newHBpairs}$ returns the new event $\eZ$ paired
    with each event $\eA$ which has happened before each of the $\eY$ on the
    same process $p$.

Most of this is the same for the deliver rule, except for the second argument
to $\text{newHBpairs}$, which for the deliver rule includes an additional
element. The other differences are: The event $e$ is a deliver event and
there's additional premise.

\footnotesize
\begin{prooftree}
    [deliver]
    \AxiomC{$ e = \text{Deliver}_p(m) $}
    \AxiomC{$ e \notin E $}
    \AxiomC{$ \text{Broadcast}(m) \in E $}
    \TrinaryInfC{$\begin{aligned}
        (E,M,H) \Rightarrow
  (  & E \cup \{e\}
  ,\\& M[p \mapsto ( [e] \mdoubleplus M[p] )]
  ,\\& H \cup \text{newHBpairs}(H,[\text{Broadcast}(m)]\mdoubleplus M[p],e)
  \quad )
    \end{aligned}$}
\end{prooftree}
\normalsize

The deliver rule corresponds to a particular process $p$ delivering an already
broadcast message $m$ to itself.
This rule is similar to the broadcast rule, but an additional premise requires
that the broadcast of $m$ already exists, and that broadcast event is included
in the second argument to \text{newHBpairs}.
The execution is updated in ways similar to that of the broadcast rule, except:

* The new event has pairs added to $H$ to indicate that it occurs after every
  event $M[p]$ on the same process $p$ and also after $\text{Broadcast}(m)$.
  Compared to the call in the broadcast rule, $\text{newHBpairs}$ returns
  these additional pairs:
  * The first clause of $\text{newHBpairs}$ returns one additional pair
    $\text{Broadcast}(m) \hb \eZ$.
  * The second clause of $\text{newHBpairs}$ returns the additional pairs of
    $\eZ$ with with each of the transitive dependencies of
    $\text{Broadcast}(m)$.

# Causal delivery

These rules do not address the causal order of messages. It's possible to
deliver a message without it's causal dependencies being satisfied. With the
definitions given we can write down the definition of causal delivery almost as
the original definition:

\begin{equation}
\begin{aligned}
    \text{observesCausalDelivery}&(\quad(E,M,H)\quad) =
                     \\  \forall & \text{Broadcast}(m) \in E
                     \\          & \text{Broadcast}(m') \in E
                     \\          & \text{Deliver}_p(m) \in M[p]
                     \\          & \text{Deliver}_p(m') \in M[p]
                                 .
                     \\          & \quad \text{Broadcast}(m) \hb \text{Broadcast}(m')
                     \\          & \quad \implies \text{Deliver}_p(m) \hb \text{Deliver}_p(m')
\end{aligned}
\end{equation}

In the definition above $e \hb e'$ can be interpreted as $(e, e') \in H$.

# Reachable execution

For mechanization, we need a way to talk about reachable executions.
A well formed execution isn't something that James Wilcox calls "type correct"
but rather something which is a result of the application of the inference
rules above.
In math you'd use a transitive reflexive closure over the transition relation,
$\text{Execution} \Rightarrow \text{Execution}$, and then define reachable as a
state which can inhabit the right side:

\begin{equation}
\begin{aligned}
        \text{Execution} \Rightarrow^\ast \text{Execution}
    \\
    \\  \text{reachable}(x) = \text{execution}_0 \Rightarrow^\ast x
\end{aligned}
\end{equation}

However, since in haskell we translate the semantics in the previous section to
a function, it is difficult to define the transitive reflexive closure. It
would be a function returning an infinite list.

One way around this is to state reachability differently.
Let's state reachability in terms of the existence of a finite sequence of rule
applications. ^[This is strongly inspired by Yiyun and James' approach to
defining "strong convergence" as the ability to apply permutations of a finite
sequence of operations and obtain the same result. In that proof they forall'd
a finite sequence of operations into existence.]
Imagine we have a $\text{Rule}$ type which roughly corresponds to the
$\text{Event}$ type defined at the beginning of the document (it is distinct in
that the rule type always says which process performs the action), and also a
function $\text{applyRules}(\text{rules})$ which takes a sequence of rules to an
execution value. Then you can state a reachable execution as the deterministic
sequence of rules required to produce it.

\begin{equation}
\begin{aligned}
    \text{reachable}(x) = \exists r:[\text{Rule}]. \quad \text{applyRules}(r) = x
\end{aligned}
\end{equation}

This is more or less how a reachable execution could be defined in Liquid
Haskell. There are a few details left out, which we'll get to in the section
about being "guarded by a predicate".

# POPL22 Theorem 1

The type of a deliverable predicate, which answers the question *is this
message ready to be delivered at the process?*, can be defined:

\begin{equation}
\begin{aligned}
    \text{DeliverablePredicate} = \text{ProcessState} \rightarrow \text{Bool}
\end{aligned}
\end{equation}

The $\text{delivered}$ predicate, which answers the question *has this
message already been delivered at the process?*, can be defined as a predicate
over the process state which returns true when a particular message's delivery
event is contained. In haskell:
```haskell
delivered m s = or $ map (eventDeliversMessage m) s

eventDeliversMessage message (Deliver _p m) = message == m
eventDeliversMessage message (Broadcast _m) = False
```

## Causally safe

The causally safe property of a deliverable predicate can be stated with the
help of the delivered predicate as:

\begin{equation}
\begin{aligned}
             \forall & m_1 : m, m_2 : m, s : \text{ProcessState},
    \\               & \text{deliverable} : \text{DeliverablePredicate}.
    \\        & \quad \text{Broadcast}(m_1) \hb \text{Broadcast}(m_2)
    \land       \text{deliverable}(m_2, s)
\\& \quad \quad \implies    \text{delivered}(m_1, s)
\end{aligned}
\end{equation}

Notice the use of $e \hb e'$ here requires extracting $H$ from the execution, or
at least some information about it.

## Guarded by Deliverable

Stating the property that an execution is "guarded by" a deliverable predicate
is more or less the same as adding an extra premise to the deliver rule in the
semantics described earlier. Also, when we discussed reachability we neglected
to mention the premises of rules!

Recall the $\text{Rule}$ type which corresponds to the $\text{Event}$ type
defined at the beginning of the document but always says which process performs
the action. Also recall the function $\text{applyRules}(\text{rules})$
which takes a sequence of rules to an execution. There's a missing piece here,
which was crucial for reachability but ignored before: A state is only
reachable if in the course of applying each rule in the sequence, that rule's
premises are met for the current execution. We can encode this by returning a
`Maybe` execution from the `applyRules` function.

First the types of the semantics and premise-test in Liquid Haskell:

```haskell
premisesHold :: Rule -> Execution -> Bool
premisesHold = -- for each rule, encoded the premises described above

semantics :: Rule -> {x:Execution | premisesHold r s} -> Execution
semantics = -- for each rule, encode the semantics described above
```

Next the `applyRules` function in Haskell:

```haskell
applyRules :: [Rule] -> Maybe Execution
applyRules rules = foldr applyRulesHelper (Just execution0) rules

-- | Apply the rule via the semantics to the execution, unless the premises do
-- not hold or there's already no execution.
applyRulesHelper :: Rule -> Maybe Execution -> Maybe Execution
applyRulesHelper _rule Nothing = Nothing
applyRulesHelper rule (Just execution) =
    if premisesHold rule execution then Just (semantics rule execution) else Nothing
```

Now all there is to do is to parameterize all of that with an extra
$\text{DeliverablePredicate}$ function argument which is called in the `Deliver`
case of the `premisesHold` function.^[I implemented this, as well as another
approach which is a bit more messy. I'm not sure which is best.]

## Theorem

That's all the pieces needed to write down Theorem 1 from our POPL22
submission. The only remaining trick which goes here is the use of the
$\text{reachable}(x)$ in the Liquid Haskell type of Theorem 1. For that, check
out the code:

<https://github.com/lsd-ucsc/cbcast-lh/blob/92d4fbcccf5442eba4e6aecb7b13e86acabda287/lib/Causal/CBCAST/Theorem1.hs#L252-L281>
