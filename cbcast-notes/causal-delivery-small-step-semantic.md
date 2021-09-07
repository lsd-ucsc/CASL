---
header-includes: |
    \usepackage{amssymb}
    \usepackage{bussproofs}
title: Causal Delivery small-step semantics
...

This document explores a minimal semantics for causal delivery.


## First attempt

This one is a bit weird because the send rule also delivers at the sender process.

### Types

delivered :: Set (Process $\times$ Message)

requires :: Set (Message $\times$ Message)

### Rules

\footnotesize
$p \in \text{Process}$  
$m' \in \text{Message}$  
$m' \notin \{ m |
                (\ast, m) \in \text{delivered}
        \lor    (m, \ast) \in \text{requires}
        \lor    (\ast, m) \in \text{requires}
\}$
^[Is actually a \emph{new} message, fresh from the ether, not one from either relation.]  
$\text{delivered}' = \text{delivered} \cup \{ (p,m') \}$  
$\text{requires}' = \text{requires} \cup \{ (m',m) | (p,m) \in \text{delivered} \}$
\begin{prooftree}
    [send]
    \AxiomC{}
    \UnaryInfC{$
        (\text{delivered},\text{requires}) \rightarrow_\text{CD}
        (\text{delivered}',\text{requires}')
    $}
\end{prooftree}
\normalsize

\footnotesize
$p_0 \in \text{Process}$  
$p_1 \in \text{Process}$  
$p_0 \neq p_1$
^[A message delivers to itself in the send rule.]  
$m' \in \text{Message}$  
$(p_0,m') \in \text{delivered}$
^[Is actually a message sent by some process (not necessarily $p_0$) and \emph{not} a new message.]  
$(p_1,m') \notin \text{delivered}$
^[Is a message not yet delivered at $p_1$ (property: exactly once delivery).]  
$\{m|(m',m) \in requires\} \subseteq
 \{m|(p_1,m) \in delivered\}$
^[Deliverable predicate (property: causal delivery).]  
$\text{delivered}' = \text{delivered} \cup \{ (p_1,m') \}$
\begin{prooftree}
    [deliver]
    \AxiomC{}
    \UnaryInfC{$
        (\text{delivered},\text{requires}) \rightarrow_\text{CD}
        (\text{delivered}',\text{requires})
    $}
\end{prooftree}
\normalsize

\pagebreak



## Second attempt

This one is simpler.

### Types

delivered :: Set (Process $\times$ Message)

requires :: Set (Message $\times$ Message)

### Rules

\footnotesize
$p \in \text{Process}$  
$m' \in \text{Message}$  
$m' \notin \{ m |
                (\ast, m) \in \text{delivered}
        \lor    (m, \ast) \in \text{requires}
        \lor    (\ast, m) \in \text{requires}
\}$
^[Is actually a \emph{new} message, fresh from the ether, not one from either relation.]  
$\text{requires}' = \text{requires} \cup \{ (m',m) | (p,m) \in \text{delivered} \}$
\begin{prooftree}
    [send]
    \AxiomC{}
    \UnaryInfC{$
        (\text{delivered},\text{requires}) \rightarrow_\text{CD}
        (\text{delivered},\text{requires}')
    $}
\end{prooftree}
\normalsize

\footnotesize
$p \in \text{Process}$  
$m' \in \text{Message}$  
$m' \in \{ m | (m,\ast) \in requires \}$
^[Is actually a message sent by some process and \emph{not} a new message.]  
$(p,m') \notin \text{delivered}$
^[Is a message not yet delivered at $p$ (property: exactly once delivery).]  
$\{m|(m',m) \in requires\} \subseteq
 \{m|(p,m) \in delivered\}$
^[Deliverable predicate (property: causal delivery).]  
$\text{delivered}' = \text{delivered} \cup \{ (p,m') \}$
\begin{prooftree}
    [deliver]
    \AxiomC{}
    \UnaryInfC{$
        (\text{delivered},\text{requires}) \rightarrow_\text{CD}
        (\text{delivered}',\text{requires})
    $}
\end{prooftree}
\normalsize

\pagebreak

# Concerns

* Too high level? Not clear how to relate to an CBCAST-execution (history and sequences and stuff)
* Can you convert the CBCAST state (TBD) into the abstract semantics state?
