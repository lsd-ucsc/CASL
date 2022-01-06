---
header-includes: |
    \usepackage{amssymb}
    \usepackage{bussproofs}
title: Process-local causal delivery
...

--- 

This document lays out what we mean by process-local causal delivery.

# System model

We model a distributed system as a finite set of $N$ $nodes$ $p_i, i : 1..N$.
Nodes behave as though they are single threaded, and are made up primarily of
two components: the user application (UAP) and the message passing algorithm
(MPA).
The UAP decides when to send messages, message content, and when to read
messages.
The MPA handles sending messages via the network on behalf of the UAP, receipt
of messages from other nodes on the network, and determining when received
messages may be delivered (made available to be read by the UAP).
The operations of the UAP are opaque and our concern is with the behavior of the
MPA.

# Goal

We'd like to describe an MPA and prove that it never violates causal delivery.
To do that we'll provide a definition of causal delivery and formally state
what we mean by the rest.

# Causal Delivery (global)

Here is the usual definition of causal delivery. It states that messages with a
broadcast order must be delivered in that order at each node.

$$
         \text{broadcast}(m) \rightarrow \text{broadcast}(m')
\implies \text{deliver}_p(m) \rightarrow_p \text{deliver}_p(m')
$$

This definition is missing some pieces.
It only applies in the context of an execution, but that isn't written down.
Further, the execution must contain all four of the events,
$\text{broadcast}(m)$, $\text{broadcast}(m')$, $\text{deliver}_p(m)$, and
$\text{deliver}_p(m')$ or the definition doesn't apply.
The execution itself is constructed incrementally by running some MPA on a set
of nodes.
A more complete definition of causal delivery follows. It fills in the missing
pieces described, and some more.

$$\begin{aligned}
 \forall    \quad   x            &: \text{Execution}    ,
            \\      p, q, r      &: \text{Process}      ,
            \\      m, m'        &: \text{Message}      .
            \\                   &          \text{broadcast}(m)  \in x[q]
            \\              \land&          \text{broadcast}(m') \in x[r]
            \\              \land&          \text{deliver}_p(m)  \in x[p]
            \\              \land&          \text{deliver}_p(m') \in x[p]
            \\              \land&          \text{broadcast}(m) \rightarrow \text{broadcast}(m')
            \\                   &\implies  \text{deliver}_p(m) \rightarrow_p \text{deliver}_p(m')
\end{aligned}$$

Both definitions rely on components which are defined here.

- An **Execution** is a mapping from Process to ProcessHistory. It is an oracle
  view of the global behavior of an MPA running on several nodes.
  - $x[p]$ indicates the ProcessHistory associated with the Process $p$ in the
    Execution $x$. If there is no such Process in the Execution, it indicates
    the empty ProcessHistory.
- A **Process** is an identifier comparable for equality which is otherwise
  opaque and guaranteed to be unique for each participant node running an MPA.
- A **ProcessHistory** is a sequence of Events which have occured at a Process
  over the course of running an MPA.
  - $e \in h$ indicates the Event $e$ is present in the ProcessHistory $h$.
- An **Event** is either $deliver_p(m)$ or $broadcast(m)$, indicating the
  Process where a Message is delivered, or simply that a Message was sent.
  Causal delivery is only concerned with broadcast messages.
  An MPA may associate an event with metadata.
- A **Message** is a UAP's raw message wrapped in an metadata container by the
  MPA. For the definition of causal delivery, a Message is comparable for
  equality but otherwise opaque.
- $e \rightarrow e'$ indicates the happens before relation on Events meaning one of the
  following:
  - $e$ and $e'$ both occur in the ProcessHistory $h_p$ for Process $p$ and $e \rightarrow_p e'$.
  - $e$ is a broadcast and $e'$ is a delivery of some Message $m$.
  - $e \rightarrow e^3$ and $e^3 \rightarrow e'$ for some Event $e^3$.
- $e \rightarrow_p e'$ indicates the process order relation, a subset of the
  happens before relation, for Events occurring in the ProcessHistory $h_p$ for
  Process $p$. Specifically, $e$ appears in the subset/subsequence of $h_p$
  prior to $e'$.
- We may occasionally use a shorthand for Messages, $m_1 \rightarrow m_2$, to
  relate the Events $\text{broadcast}(m_1) \rightarrow \text{broadcast}(m_2)$.

Variable names follow this convention:

* $x$, $y$, and $z$ are Executions.
* $p$, $q$, and $r$ are Processes.
* $m$, $m'$, $m_1$, and $m_2$ are Messages.
* $e$, $e'$, $e_1$, and $e_2$ are Events.

# Main theorem (informal)

All Executions produced by CBCAST^[Or something very nearly like CBCAST.]
satisfy (global) causal delivery[^additionally].

[^additionally]:
    Executions produced by CBCAST have the following additional properties,
    probably:

    * Messages are never delivered before they are sent.
    * The Process $p$ on every Event $deliver_p(m)$ in a ProcessHistory $h_p$
      is the same $p$ which maps to $h_p$ from the Execution $x$ via $x[p]$.
    * The Process $p$ mapping to a ProcessHistory $x[p]$ in the Execution $x$
      must be the Process on every $deliver_p(m)$ in that ProcessHistory.
    * TODO: select one of the two above; they say the same thing differently.

# Axioms

## Vector Clock isomorphism

Here we'll take the vector clock isomorphism as an axiom^[Thanks Gan and
Simon!]. The axiom says the less-than relation on vector clocks associated with
Events is isomorphic to the happens before relation on those Events.

$$
VC(e) < VC(e') \iff e \rightarrow e'
$$

A more complete definition fills in the missing pieces as before, though less
informative.

$$\begin{aligned}
 \forall    \quad   x            &: \text{Execution}    ,
            \\      q, r         &: \text{Process}      ,
            \\      e, e'        &: \text{Event}        .
            \\                   &          e  \in x[q]
            \\              \land&          e' \in x[r]
            \\              \land&  (       VC(e) < VC(e')
            \\                   &\iff e \rightarrow e'
                                    )
\end{aligned}$$

* $VC(e)$ indicates the vector clock associated with Event $e$, which is
  metadata produced by the MPA.
* Again, we may occasionally use a shorthand for Messages, $VC(m)$, meaning
  $VC(\text{broadcast}(m))$.

Note that the use of the happens before relation hints about the importance of
the system which constructs those vector clocks.
The vector clock isomorphism implies a strong coupling between the structure of
the Execution and the vector clocks associated with each Event.

# Local to Global theorem (informal)

If every process independently delivers the messages that it receives in an
order consistent with causality, then (global) causal delivery is satisfied.

# Causal Delivery (local)

Taking the vector clock isomorphism as an axiom we can redefine (global) causal
delivery as a local property of a single node, ignoring the distributed
interactions by which the MPA constructs vector clocks.
Instead of reasoning about the whole Execution, we will reason about each node
and its ProcessHistory separately.

$$\begin{aligned}
 \forall    \quad   h_p          &: \text{ProcessHistory}   ,
            \\      m, m'        &: \text{Message}          .
            \\              \land&          \text{deliver}_p(m)  \in h_p
            \\              \land&          \text{deliver}_p(m') \in h_p
            \\              \land&          VC(\text{broadcast}(m)) < VC(\text{broadcast}(m'))
            \\                   &\implies  \text{deliver}_p(m) \rightarrow_p \text{deliver}_p(m')
\end{aligned}$$

What changes were made from the (global) causal delivery definition?

1. The execution $x$ and references to process histories $x[q]$ and $x[r]$ were
   removed. The process history $x[p]$ is replaced by $h_p$.
1. By the vector clock isomorphism, the happens before ordering of the message
   broadcasts is expressed as the vc-less ordering of the messages' broadcast
   times.

# Process local theorem

All ProcessHistorys produced by a single instance of CBCAST satisfy (local)
causal delivery.

# Proof sketch (informal)

1. Process local theorem -- Show code satisfies local-CD.
1. Vector clock isomorphism axiom -- Transform local-CD VC-less-premise to HB-premise of global-CD.
1. Local to Global theorem (???)
1. Main theorem (goal)
