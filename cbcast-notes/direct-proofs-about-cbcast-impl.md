---
header-includes: |
    \usepackage{amssymb}
    \usepackage{bussproofs}
title: Direct proofs about CBCAST impl
...

This document explores some strategies for proving that our CBCAST
implementation implements causal delivery.

* <https://github.com/lsd-ucsc/cbcast-lh>

# Using a process history

Can we do the proof with a process history?

That is, if our transition relation `S ==> S` includes only a single process's
knowledge, is it possible to state the proof in LiquidHaskell with sufficient
conditions to prove causal delivery?
At the very least, we need to keep the history of messages which this process
has delivered, so lets start with a state `S` containing only those two
components.

```haskell
type S = (Process r, [Message r])

type T = S -> S
```

The API gives us our inference rules.

* <https://github.com/lsd-ucsc/cbcast-lh/blob/076dc25cb859af57439aa0158c1cbffc8351f16d/lib/Causal/CBCAST/Protocol.hs>

For each rule, the process argument is taken from `S`.
That is, the only actor in this system is the lone `Process r`.

```haskell
data R r
    = Broadcast{raw :: r} -- from the local application (puts a message in the process)
    | Receive{message :: Message r} -- from the network (puts a message in the process)
    | Deliver -- to the local application (gets a message from the process)
    | Convey -- to the network (gets messages from the process)
```

These rules don't have any preconditions. The transition function is:

```haskell
transition :: R -> T -- Given a rule, you have a transition function.
transition Broadcast{raw}   (p, h) = (broadcast raw p, h)
transition Receive{message} (p, h) = (receive message p, h)
transition Deliver          (p, h) = case deliver p of
                                        (p', Nothing) -> (p', h)
                                        (p', Just m)  -> (p', h++[m])
transition Convey           (p, h) = let (p', _ms) = convey p in (p', h)
```

These rules serve the same purpose as the functions they're named after:

* The `Broadcast` and `Receive` rules take the provided message and insert it
  in the `Process r` with the corresponding function.
  The history does not change.
* The `Deliver` rule attempts to retrieve an inbound message from the
  `Process r`.
  **If successful, it appends that message to the history of messages delivered
  to the local application.**
  This rule is crucial for the proof.
* The `Convey` rule attempts to retrieve outbound messages from the `Process r`
  and simply throws them out.
  It's somewhat vestigial for the purposes of the proof.

With that, we can state the theorem that our CBCAST implementation preserves
causal delivery:
```haskell
proofCausalDelivery :: r:R -> {s:S | causalDelivery s }
                           -> { causalDelivery (transition r s) }
proofCausalDelivery = () *** Admit
```

This theorem is kind of important, so here it is again written in math:
$$
\forall r \in R, s \in S.
         \texttt{causalDelivery}(s)
\implies \texttt{causalDelivery}(\texttt{transition}(r, s))
$$

Oops, we haven't defined the `causalDelivery` predicate.
That's also important.

## Defining causal delivery in terms of a process history

The `causalDelivery` predicate tests if the process history described by a
state `S` contains no violations of causal delivery. It will have this type:
```haskell
type CausalDeliveryPredicate = S -> Bool
```
Causal delivery is defined somewhat vaguely. It implicitly relies on there
being two broadcast events and two deliver events in some ill-defined
structure. The definition doesn't apply if any of the four events are missing.
$$
         \text{broadcast}(m) \rightarrow \text{broadcast}(m')
\implies \text{deliver}_p(m) \rightarrow_p \text{deliver}_p(m')
$$
The isomorphism between the happens before relation and vector clock less-than
relation is assumed to be true. This is a big assumption, but it's really a
lemma for our purposes.
$$
     \text{broadcast}(m) \rightarrow \text{broadcast}(m')
\iff \text{VC}(m) < \text{VC}(m')
$$

With those definitions we can think about ways to phrase `causalDelivery` in
terms of `S`. In particular, we will examine the process history,
`history::[Message r]`. Consider:

* The message field `mSent::VC` indicates when broadcast occurred and so
  $\text{broadcast}(\texttt{m}) \rightarrow \text{broadcast}(\texttt{m'})$
  iff `vcLess (mSent m) (mSent m')`.
* Position in the `history::[Message r]` list indicates delivery order (process
  order) and so every `(m:ms)` therein has
  $\text{deliver}_p(\texttt{m}) \rightarrow_p \text{deliver}_p(\texttt{m'})$
  for $\texttt{m'} \in \texttt{ms}$.
* Alternatively, position in the `history::[Message r]` list indicates
  delivery order and so for
  $i,j \in [0,\texttt{length}(\texttt{history}))$ where
  $\texttt{history}[i] = \texttt{m}$ and
  $\texttt{history}[j] = \texttt{m'}$ we have
  $i<j \iff \text{deliver}_p(\texttt{m}) \rightarrow_p \text{deliver}_p(\texttt{m'})$.

Applying these translations to the definition yields a frankenstatement (1):
$$
         \texttt{vcLess (mSent m) (mSent m')}
\implies \exists \texttt{(m:ms)} \in \texttt{tails}(\texttt{history}).
         \texttt{m'} \in \texttt{ms}
$$

Or alternatively, a frankenstatement with indices:
$$\begin{aligned}
&        \texttt{vcLess (mSent m) (mSent m')}
\implies
\\
&   \forall i,j \in [0,\texttt{length}(\texttt{history}))
    \text{  where  }
                \texttt{history}[i] = \texttt{m}
        \land   \texttt{history}[j] = \texttt{m'}
    .
         i < j
\end{aligned}$$

It's a little unclear how to get from this implication to a test on the
`history::[Message r]` data structure. We could try calling a function for
every pair of messages in the history.

```haskell
causalDelivery :: S -> Bool
causalDelivery (__p, h)
    -- call `helper h` for messages `h X h`
    = fmap (\m -> fmap (\m' -> causalDeliveryHelper h m m') h) h
--  = (\m -> causalDeliveryHelper h m <$> h) <$> h -- more concise, less readable
```
```haskell
causalDeliveryHelper :: [Message r] -> Message r -> Message r -> Bool
causalDeliveryHelper h m m' =
    if vcLess (mSent m) (mSent m')
    then {- implement the righthand-side of one of the implications -}
    else True
```

This approach is probably correct, though it might be a little difficult to
write a proof about.

Let's try a different approach. Recall that the happens-before relation, $ev
\rightarrow ev$, is a *strict partial order* isomorphic to the vector clock
less-than relation, $vc < vc$. Also recall the definitions of the vector clock
less-than, less-equals, and concurrent (or independent) relations:

$$\begin{aligned}
u \leq v \iff \forall i. u[i] \leq v[i]
\\
u < v \iff u \leq v \land u \neq v
\\
u || v \iff u \nless v \land v \nless u
\end{aligned}$$

The *strict*ness of the strict partial order represented in the vector clock
less-than relation comes from the clause $u \neq v$.
That clause means that $u < v$ doesn't contain the reflexive pairs $u = v$
contained in $u \leq v$.
Consequently, the reflexive pairs are also contained in $u || v$ which is more
or less defined to be everything not in $u < v$.
So if you combine the $u < v$ and $u || v$ relations you obtain a total order
(because now all pairs of vector clocks exist in the relation) which is
compatible (because no pair existed in both of the constituent relations).

$$
u \leq^* v \iff u \leq v \lor u || v
$$

> **Aside** Actually, this is not a total order. Nathan and I were discussing
> about whether a total order requires the relation to be "transitive". The
> concurrency relation is not transitive ^[Virtual Time and Global States].

I think this new relation $u \leq^* v$ is a linear extension of $u < v$. If we
agree about that, then we can define `causalDelivery` as a predicate on the
sortedness of `history::[Message r]`. However, instead of just being "sorted
by $u \leq^* v$" I think we need a stronger statement because $u || v$ is not
transitive.

> **For Example** If we have $[a, z, q, r]$ then saying that it's sorted by $u
> \leq^* v$ would be like saying:
> $$a \leq^* z \land z \leq^* q \land q \leq^* r$$
> That's not enough because we don't know $a \leq^* q$ and some others because
> we lack transitivity. Instead, I'd like to make a statement that it's "super
> sorted" by $u \leq^* v$ meaning:
> $$\begin{aligned}
>          a \leq^* z \land a \leq^* q \land a \leq^* r
> \\ \land z \leq^* q \land z \leq^* r
> \\ \land q \leq^* r
> \end{aligned}$$

The property could be summarized: all heads are related to all elements of their
tails by $u \leq^* v$.
I guess you can write this "super sorted" property^[The LiquidHaskell blogpost
on abstract refinements calls this "pairwise related". Then they call that
terminology itself "sloppy". So I don't know what to call it.
(<https://ucsd-progsys.github.io/liquidhaskell-blog/2013/07/29/putting-things-in-order.lhs/>)]
as a frankenstatement:

$$
\forall \texttt{(m:ms)} \in \texttt{history}.\quad
\forall \texttt{m'} \in  \texttt{ms}.\quad
\texttt{m} \leq^* \texttt{m'}
$$

And this is easy to implement more or less exactly in haskell^[Gan: It's still a cross product.]:

```haskell
causalDelivery' :: S -> Bool
causalDelivery' (_p, h) = all go (tails h)
  where

    go :: [Message r] -> Bool
    go = \case
        (m:ms) -> all (relation m) ms
        [] -> True

    relation :: Message r -> Message r -> Bool
    relation m m' = mSent m `vcLess` mSent m' || mSent m `vcIndependent` mSent m'
```

Cool, so that's 3 strategies, at least, for defining `causalDelivery`. What do
we think is most defensible? What do we think is easiest to write a proof
about?

---
\pagebreak
# Using a global view

I don't really have time left in the evening to flesh this all out. The key things are:

1. Transition relation state `S2`
1. Rule data type constructors
   * Are there any premises? Yes. Messages must be drawn from places that make
     sense. Processes must be drawn from the pool of extant processes.
1. Transition function `transition2`
1. New definition of `causalDelivery2`

If we can't do the proof with a process history, then perhaps we can
complete it with partial executions. Imagine a state `S2` like this:

```haskell
data S2 = S2
    { inflight :: [Message r]
    , processes :: [(Process r, [Message r])]
    }
```

The new rule type has a process for each action.

```haskell
data R2 r
    = Broadcast{raw::r, process::Process r}
    | Receive{message::Message r, process::Process r}
    | Deliver{process::Process r}
    | Convey{process::Process r}
```

Now we care about where messages are coming from and where they are going.

```haskell
premisesHold :: R2 -> S2 -> Bool
premisesHold Broadcast{raw,process}     S2{inflight,processes} = -- process in processes
premisesHold Receive{message,process}   S2{inflight,processes} = -- process in processes
                                                              && elem message inflight
premisesHold Deliver{process}           S2{inflight,processes} = -- process in processes
premisesHold Convey{process}            S2{inflight,processes} = -- process in processes
```

The transition relation type should ensure that premises hold.

```haskell
transition2 :: r:R2 -> {s:S2 | premisesHold r s} -> S2
transition2 Broadcast{raw,process}     S2{inflight,processes}
    = -- call `broadcast raw process` and update into processes
transition2 Receive{message,process}   S2{inflight,processes}
    = -- call `receive message process` and update into processes
      --
      -- if all the processes have received it (how to check this?)
      -- then remove the message from inflight
transition2 Deliver{process}           S2{inflight,processes}
    = -- call `deliver process` and update into processes
      --
      -- if a message is returned then put it into the history for the process
transition2 Convey{process}            S2{inflight,processes} =
    = -- call `convey process` and update into prcesses
      --
      -- put the messages into inflight
```

Now all the rules make essential changes to `S2`. The proof is going to be
correspondingly more complex. It might be easier to write `causalDelivery2`:

```haskell
causalDelivery2 :: S2 -> Bool
causalDelivery2 = ?
```

Also, the theorem changes:

```haskell
proofCausalDelivery2 :: r:R2 -> {s:S2 | premisesHold r s && causalDelivery2 s }
                           -> { causalDelivery2 (transition2 r s) }
proofCausalDelivery2 = () *** Admit
```
