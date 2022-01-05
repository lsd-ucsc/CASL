# Research progression

Discussion of runtimes for lazy languages. Progression through the years.

## "Naive" graph translation

- Represent program as a graph and do explicit graph manipulations to evaluate
  lazy programs
- Didn't scale; viewed as a toy

## S K I graph evaluation

- **David Turner** showed that S K I combinators could be used for efficient
  evaluation of lazy code. Each function is compiled to pre-determined set of
  combinators which are interpreted at runtime.
- _A New Implementation Technique for Applicative Languages_ by
  **David Turner**
    - Technique was used to implement the lazy language Miranda.
    - Founded company "Research Software LTD".
- Other branches of research:
    - Template Instatiation
        - jose: "think of it as inventing combinators at compile time based on
          the input program"

## G-Machine

- The "graph" machine inspired by S K I combinators with some additional
  complexity, but vastly more efficient. Each function is compiled to machine
  code which when run performs the graph transformation.
- _The Chalmers Lazy-ML Compiler_ by **L Augustsson and T Johnsson**
    * Written after they graduated. Their theses both contain some of the work
      on G-machine.
- _Parallel Graph Reduction with the <ν, G>-machine_ by **L Augustsson and T
  Johnsson**
    * Additional work on parallelization of G-machine.
- Other branches of research:
    - _A B C machine_, used by the lazy language _clean_
        - A bit more on top of the S K I
        - [ ] What is the advantage or insight?
    - _BWM, a concrete machine for graph reduction_ by **L Augustsson**
        - jose: "graph reduction is expensive, what if we could load it in big words?"
    - _The Reduceron, Widening the von Neumann Bottleneck for Graph Reduction using an FPGA_ by **M Naylor and C Runciman**
        - jose: "and what if graph reduction was cache aware?"
        - jose: a group made an FPGA which turned out to be very fast [and
          might probably be significantly faster than GHC if made into an IC]
        - Project website: <https://www.cs.york.ac.uk/fp/reduceron/>

## SG-Machine

- _The Implementation of Functional Programming Languages_ by **S P Jones**
    - "Spineless" G-machine, small additional complexity for a ton of
      performance.
    - jose: probably a sweet spot in tradeoff of complexity and performance

## STG-Machine

- Spineless "Tagless" G-machine, more complex, very different from
  predecessors.
- Complexity moved from the instruction set to the evaluation.
    - jose: famously used in GHC
    - both: it could be a local maxima?

# Optimizations on lazy functional languages

- jose: some translations are always wins
- _Unboxed values as first class citizens in a non-strict functional language_
  by **S P Jones and J Launchbury**
- _The worker/wrapper transformation_ by **A Gill and G Hutton**
    * Requires unboxed-values
    * Idea:
        * function returns a pair, used in a loop, and now you're
          allocating & destructing over and over (heap churn)
        * transformation:
            - worker: does the work w/o allocating
            - wrapper: identifies the final result and allocates on the heap
              for rest of program

# Other questions

* Can we unbox everything? Can we put everything on the stack?
  ```
  caller {
      callee {
          allocate and return
      }
      drop
  }
  ```
  Can we put the allocation in the callee to the stack frame of the caller?
    * jose: historically, for lazy languages we want sharing
        * Eg. For `\f a -> (a, a))` we want to only evaluate `a` once
        * Eg. What if we say things are linear? (substructural)
    * _Linearity and Laziness_ by **D Wakeling and C Runciman**
        * jose: Runciman did a "let's make a lazy language that's
          linear"
    * jose: what about separate compilation?
    * jose: what if caller is map (and callee is a function?)
    * jose: why not exploit strictness analysis?
* Should i just make it strict?
    * jose: Max Bolingbroke did a "what if we had a strict core for haskell,
      and then figured out what needs to be lazy?"
    * _Types are calling conventions_ by **M Bolingbroke and S P Jones**
    * jose: either way you have a hard problem to solve
        * jose: if you start with everything lazy, you need strictness analysis
            * you might introduce a bottom that doesn't exist
        * jose: if you start with everything strict, you have terrible thunks
            * eg. the "wrap with lambda and insert a call" is slow

# Disorganized thoughts

* jose: think in terms of deltas; have a platform and think about what it takes
  to extend that to something else (eg. Augustsson's G-machine impl was used in
  many other works by him?)
* patrick:
    * SG-Machine or BigWordMachine are candidates maybe?
    * Handle deallocs with Linearity bleeding to ReferenceCounting
        * Linearity to reduce allocations
        * Linearity to inject drops/deallocs
        * ReferenceCounting to pick up the remainder
    * Types are calling conventions to combine laziness and strictness
        * Strictness allows for more use of the stack (fewer heap allocations)
        * jose: knows strictness analysis
