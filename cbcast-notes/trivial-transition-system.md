---
header-includes: |
    \usepackage{amssymb}
    \usepackage{bussproofs}
title: Trivial transition system
...

## Define "Nat" to model $\mathbb{N}$ with the $\mathbb{Z}$

Types.

$$
\text{Nat} = \mathbb{Z}
$$

Initial value.

$$
\text{init} = 0 : \text{Nat}
$$

Inference rules.

\footnotesize
\begin{prooftree}
    [add]
    \AxiomC{$ m : \text{Nat} $}
    \AxiomC{$ n : \mathbb{Z} $}
    \AxiomC{$ 0 \leq n $}
    \TrinaryInfC{$ m \rightarrow m + n $}
\end{prooftree}
\normalsize

\footnotesize
\begin{prooftree}
    [sub]
    \AxiomC{$ m : \text{Nat} $}
    \AxiomC{$ n : \mathbb{Z} $}
    \AxiomC{$ 0 \leq n $}
    \AxiomC{$ n \leq m $}
    \QuaternaryInfC{$ m \rightarrow m - n $}
\end{prooftree}
\normalsize

If you take the transitive reflexive closure of $\rightarrow$ you can show $0
\leq m$ for all Nat reachable from init.
