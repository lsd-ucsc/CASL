# 2020-07-10, group meeting

* linsey: let's talk about gan+patrick's pair programming project
    * gan: we're trying to figure out how to implement an editor
    * patrick: churning between 2d representation of editor content and 1d
      representation of editor content
    * lindsey: i had roommates/friends who wanted to implement a collaborative
      editor, and learn haskell.. they were implementing in a modular way, with
      a plugin system, where plugins could change the behavior of keystrokes..
    * patrick: we're going for a minimal approach for exploring collaborative
      editing
    * gan: for this week, i implemented RGA, and now i understand it better..
      in gomes-et-al they said that the unique ids must [correspond to
      causality].. in short, this guarantees that concurrent inserts are
      commutative..
    * patrick: can you use a uuid and sort by that and still satisfy this
      requirement?
    * [more discussion, typist couldn't keep up]
    * gan: here's a link to the RGA impl
      <https://github.com/lsd-ucsc/editor/blob/interpret/src/CRDT/RGA.hs>
* lindsey: did you look at the original paper, or in gomes-et-al?
    * gan: gomes-et-al
    * lindsey: the thing about "RGA requires the IDs to reflect the total order
      of causality" was news to me when reading the gomes-et-al paper [typist
      couldn't keep up]
    * lindsey: there's a similar thing in the amazon dynamodb paper, which they
      don't make explicit, but they do use vector clocks to determine order of
      events, to reduce the number of causal anomalies
    * lindsey: the RGA requirement is similar
* lindsey: in undergrad dist sys class, we discuss different kinds of logical
  clocks relationships and being "consistent with causality"
    * lamport clocks give you one direction but not the other [typist could not
      keep up]
* lindsey: hi farhad!
    * farhad: this similar to last week's paper, but you can define the
      consistency model
    * lindsey: this paper is more general, because it allows examining a
      variety of consistency models
    * lindsey: the other major difference, is the automated aspect of this
      paper, it can be done with z3
    * lindsey: whereas with the gomes paper, more is done by hand,
      mechanised-proof-assistant, rather than a theorem prover; but also the
      set of assumptions in the gomes paper is very small, you start with a
      small set of axioms
* lindsey: with the cav19 paper, there's a lot of math that's done outside
  of the theorem prover to establish ...
* lindsey: [typist couldn't keep up] the whole idea was checking
  "noninterference to commutativity" (NTC) against a given consistency property..
  this part is automated
* lindsey: is it actually the case that NTC implies SEC? that's what they
  proved on paper, and that's their primary contribution
    * lindsey: the gomes paper [throws shade on] these efforts that use
      proofs outside of ITP or ATP tools
    * lindsey: there may be bugs in the NTC->SEC proofs
    * lindsey: there's a paper about this issue, called [run your
      research](https://users.cs.northwestern.edu/~robby/lightweight-metatheory/popl2012-kcdeffmrtf.pdf),
      [POPL2012 talk about run your
      research](https://youtube.com/watch?v=BuCRToctmw0)
    * lindsey: PLT redex will let you write down your language
      specification and get an implementation; "lightweight mechanization"
      lets you find bugs in your math easily (lightweight because you're
      not proving anything)
    * lindsey: this cav19 paper, i don't know if redex would have been an
      appropriate tool, but this paper is almost not PL; it's like a big
      transition system
    * gan,farhad,patrick,lindsey: there aren't enough examples of the
      transition system, they change from their signature in the
      "illustrated examples" section to another
* lindsey: there's different ways to view this paper
    * lindsey: we could take you at your word that theorem 1 is true, treat it
      as a black box, and then build on top of it
    * lindsey: another way to use this paper, would be to try to understand
      their proof technique, by digging in more deeply
    * lindsey: another thing to keep in mind is that the CAV audience,
      which is distinct from a PL or a dist-sys audience
    * lindsey: eg. contrast with the original CRDT paper, which discusses
      replicas; it's very common to discuss replicas in a dist-sys conference;
      if you're going to talk about replicas, you need to have a fixed number
      or have some affordance for replicas to leave and join
    * lindsey: none of that is part of [the cav19] formalism; they don't have
      any of that, they have a big transition system with a "soup" of things
      that are happening .. and use some [paraphrased: weedle words/phrases] to
      relate it back to [the concrete]
    * patrick: is that a weakness? i didn't notice because i don't know what's
      standard
    * lindsey: not a weakness, it's a choice.. it's our job as researchers to
      be aware of these choices; be aware that they made the choice not to talk
      about replicas

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
