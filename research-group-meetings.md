# 2020-07-03, group meeting

* lindsey: proof left undone in the UMD LH paper is for the causal tree
    * lindsey: the gomes paper RGA proof might have insights
    * gan: about the UMD project, what network is defined?
    * lindsey: the UMD paper doesn't have any network axioms
    * lindsey: as you saw from this paper, those assumptions are necessary for
      some CRDTs but not others; eg. for the counter, causal delivery is
      unnnecssary
    * lindsey: next paper will include this discussion
    * lindsey: [typist couldn't keep up]
* lindsey: overall point, it's interesting that the RGA CRDT in the gomes paper
  is the one that is most difficult to prove convergence of because it works on
  sequences.. sequences make everything hard.. why? if you're inserting
  something into a list, and you're concurrently inserting something else, the
  indices change.. or if you insert and delete, these operations don't commute
  in general.. and the reason why this matters: they have this requirement that
  if you delete an element, the element must exist.. what if you're deleting an
  element and concurrently somebody else is concurrently inserting?
    * lindsey: maybe we need to look carefully at what they did, in the
      accompanying artifact
    * patrick: i felt that i couldn't keep track of all the details in the
      paper (much less the artifact) because i don't really know isabelle
    * lindsey: [typist couldn't keep up]
    * lindsey: it is important to understand what they gain from the automation
* lindsey: i don't think there's a clear cut distinction between automated and
  manual proving tools
    * lindsey: what is it that the human has to do, and what is it that the SMT
      is doing for you
    * patrick: i think we need to learn multiple tools to answer those
      questions
    * lindsey: there's a paper, "A Tale of Two Provers" that compares liquid
      haskell and COQ; it's a few years old now; when i first read it, i didn't
      get as much as i thought I would out of it
    * lindsey: i think it's worthwhile to sample these tools and get a sense
      about them; i haven't seen a broad survey comparison of what they all do
    * patrick: maybe finding examples to translate as a group
    * [typist couldn't keep up]
    * lindsey: they all use both proof and code
        * gan: coq has code
        * lindsey: isabbelle, everything that starts with fun is code
        * lindsey: LH is special because it's an practial language with the
          proofs added on
* lindsey: the LH group originally described using the LH technique to "turn
  any language into a theorem prover" in a paper that didn't get published:
  <https://arxiv.org/abs/1610.04641>
    * patrick: go is simple and could be a testbed for this
    * lindsey: at virtual PLDI i spoke with a person using COQ to prove things
      about go programs: <https://www.chajed.io/papers/goose:coqpl2020.pdf>
    * lindsey: he said "how do we convince systems people of the importance of
      tools like COQ"?
    * lindsey: [i'm coming at it from the opposite perspective]
    * lindsey: this is a short paper, their tool is called "goose" which is a
      fun pun
    * lindsey: can you really do LH approach with any language?
    * gan: maybe because haskell is a functional language it's easier?
    * lindsey: is there any way between these two approaches [typist couldn't
      keep up] to do this with another language?
* lindsey: i got an email from somebody recently who was asking for critique of
  [a systems teaching approach very distinct from lindsey's which is
  concepts-first]
    * lindsey: [typist couldn't keep up; discussion of how to start learning
      distributed systems]

# 2020-06-26, group meeting

* lindsey: dkvs repo future is unknown
    * gan,patrick: let's try developing on forks to move quickly and explore
* lindsey: liquid-haskell is now a ghc-compiler plugin (as of 8.10.1)
    * gan has already started digging into the new liquid-haskell ghc-compiler
      plugin
      [tutorial](https://github.com/ucsd-progsys/liquidhaskell/blob/develop/src/Language/Haskell/Liquid/GHC/Plugin/Tutorial.hs)
        * gan: the hackage liquid-haskell is out of date
        * gan: to play with the plugin, you need to clone the git repo
    * gan is looking to try development of liquid-haskell
        * lindsey: look at the code, look at the mailing list, etc.. unsure
        * lindsey: potentially look at the typeclasses for liquid-haskell paper
* lindsey: re discussion about lifting
    * lindsey: i incorrectly used the work reflection, the right phrasing is
      "lifting haskell functions into refinement types"
    * lindsey: classic example is referring to the length of a list (using the
      haskell function length) in the type
    * lindsey: two ways to do this in liquid-haskell, measure and reflect
        * the measure annotation lets you refer to some code in a type, but
          code isn't analyzed; unsure
        * the reflect annotation actually does some evaluation of code at
          typechecking time
    * lindsey: there's a popl18 paper that discusses reflect, but nikki's
      dissertation might be better
    * lindsey: it's also worthwhile to look at kaplan (caplan?) which is
      kaplan:scala::liquidhaskell:haskell
        * kaplan gives up on decidability
        * liquid-haskell goes to greath lengths to keep things decideable
        * there are circumstances where you're ok with undecidability
    * farhad: looked at the refinement-reflection paper and am looking at
      (inaudible) kaplan paper (inaudible)
        * lindsey: figuring out how to handle the lifting for gallifrey is
          going to be a long term project
        * lindsey/farhad: googling for this will get you Dr. Who references
        * lindsey: the link is https://www.cs.cornell.edu/~milano/project/gallifrey/

# 2020-06-22, group meeting

* let's read the 'gomez et al' 'verifying sec' paper this week
    * notable because they modeled the network in addition to their data
      structures
        * including the fact that messages can be slow, or lost
        * network conditions aren't parameterized, but are part of the proof
* we should be signing up for fall courses
