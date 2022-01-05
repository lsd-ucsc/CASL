---
header-includes: |
    \usepackage{amssymb}
    \usepackage{bussproofs}
title: Process-local causal delivery
...

This document lays out what we mean by process-local causal delivery.

## Causal Delivery (globally)

We'd like to prove that CBCAST, or something like it, doesn't violate Causal
Delivery, as defined:

$$
         \text{broadcast}(m) \rightarrow \text{broadcast}(m')
\implies \text{deliver}_p(m) \rightarrow_p \text{deliver}_p(m')
$$

There are some ambiguous components to this definition.
It only applies in the context of an **execution** which contains all four of
the events, $\text{broadcast}(m)$, $\text{broadcast}(m')$,
$\text{deliver}_p(m)$, and $\text{deliver}_p(m')$.
If any of those events aren't present for some instantiation of the definition
for $m$, $m'$, and $p$ in some execution, then that instantiation doesn't
provide evidence for or against the theorem holding.
A more complete definition of the theorem we want to prove
might be:

$$\begin{aligned}
    \forall \quad x &: \text{Execution},
 \\    p, q, r      &: \text{Process},
 \\               m &: \text{Message},
 \\              m' &: \text{Message}.
 \\      &   \text{broadcast}(m) \in x[q]
 \\ \land&   \text{broadcast}(m') \in x[r]
 \\ \land&   \text{deliver}_p(m) \in x[p]
 \\ \land&   \text{deliver}_p(m') \in x[p]
 \\ \land&   \text{broadcast}(m) \rightarrow \text{broadcast}(m')
 \\      &\implies      \text{deliver}_p(m) \rightarrow_p \text{deliver}_p(m')
\end{aligned}$$

This still leaves some points ambiguous:

* The type of an execution is something like `Map Process (List Event)`. An
  event is either `Broadcast Message` or `Deliver Process Message`.
* $e \rightarrow e'$ is happens-before. We can extend it to apply not only to
  events but also to messages by saying that $m_1 \rightarrow m_2$ is the
  shortened form of $\text{broadcast}(m_1) \rightarrow \text{broadcast}(m_2)$.
* $e \rightarrow_p e'$ is process-order. This is the subset of happens-before
  for events $e$ and $e'$ both occurring on process $p$.

Variable names follow this convention:

* $x$, $y$, and $z$ are executions.
* $p$, $q$, and $r$ are processes (or process identifiers).
* $m$, $m'$, etc are messages.
* $e$, $e'$, etc are events.

# Axioms

Gan and Simon have taken on the vector clock isomorphism proof work, and so
we'll take it as an axiom here. The definition is:

$$
VC(e) < VC(e') \iff e \rightarrow e'
$$

Again there are some ambiguities here. I won't spell them all out, but the use
of the happens-before relation is a hint about the importance of the system
which constructs those vector clocks. Executions and the vector clocks within
them are coupled tightly by the isomorphism.

# Causal Delivery (locally)

Without speaking formally, if every process independently delivers the messages
that it receives in an order consistent with causality, then the global causal
delivery definition above must hold.^[TODO: Say this formally.]
If we take the vector clock isomorphism as an axiom we can redefine causal
delivery as a property of a single process, ignoring the process which
constructs the vector clocks.

$$\begin{aligned}
    \forall \quad h_p &: \text{ProcessHistory},
   \\               m &: \text{Message},
   \\              m' &: \text{Message}.
 \\ \land&   \text{deliver}_p(m) \in h_p
 \\ \land&   \text{deliver}_p(m') \in h_p
 \\ \land&   VC(m) < VC(m')
 \\      &\implies      \text{deliver}_p(m) \sqsubset^p \text{deliver}_p(m')
\end{aligned}$$

What changes were made from the causal delivery (global) definition?

1. The execution $x$ and references to process histories $x[q]$ and $x[r]$ were
   removed. The process history $x[p]$ is replaced by $h_p$.
1. By the vector clock isomorphism, the happens-before ordering of the message
   broadcasts is expressed as the vc-less ordering of the messages' sent
   times.
1. The process order relation on events in the process history at $p$ is
   replaced with comes before relation^[Gomes' Verifying SEC], $e \sqsubset^p
   e'$, which interrogates $h_p$.

# Types and functions

## ProcessHistory

Process history is represented as a `List Event` which has a small interface:

* `phEmpty :: ProcessHistory` is an empty process history, represented by the
  empty list.
* `phHas :: ProcessHistory -> Event -> Bool` returns true when the process
  history contains the given event.
* `phPriorTo :: ProcessHistory -> Event -> Maybe ProcessHistory` obtains the
  history prior to a given event, if that event occurred in the history.

With these we can define comes before, $e \sqsubset^i e'$ as:

```haskell
comesBefore :: ProcessHistory -> Event -> Event -> Bool
comesBefore ph e e' =
    case phPriorTo ph e' of
        Just ph' -> phHas ph' e -- ph contains e', does e occur prior to that?
        Nothing -> False -- ph does not contain e'
```

This differs slightly^[TODO: prove these equivalent?] from the definition of comes before given by Gomes et al.

$$\begin{aligned}
e1 \sqsubset^i e2 \iff & \exists xs, ys, zs.
\\                     & xs@[e1]@ys@[e2]@zs = history(i)
\end{aligned}$$
