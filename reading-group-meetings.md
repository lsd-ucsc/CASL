These are paraphrased notes from a live discussion with several people.
Parenthesis or brackets are used where the paraphrasing is particularly distant
from what the speaker said. Feel free to edit these notes as you see fit.

# 2020-07-10, paper discussion

## Week 1 of 1 for [Automated Parameterized Verification ofCRDTs (Extended Version)](https://www.cs.purdue.edu/homes/suresh/papers/cav19.pdf)

* lindsey: what was your takeaway for what this paper was about?
    * patrick: [vague answer]
    * kamala: proving that CRDTs converge, and proving under different
      consistency guarantees, and proving the conditions under which they
      converge.. the easy way to make them converge would be to make all
      operations commutative, [typist couldn't keep up]
    * lindsey: last time we discussed the gomes paper, and they fixed on causal
      consistency that and required that concurrent operations commute
    * lindsey: this paper is so much more general, and they have a rigorous
      definition of of exactly what consistency policy is necessary for a CRDT
      to converge, and even showed that causal consistency isn't sufficient for
      some CRDTs
    * farhad: [typist couldn't keep up]
* lindsey: [sharing table of "consistency schemes" near Definition 1]
    * lindsey: [aside] cav has a short page limit, so things are jammed in
      sometimes without labels, like "table 1" with a caption
    * lindsey: [going over parts of the table]
        * the right column header is a "configuration" which defines
          constraints on [ordering of events, visibility, other things]
    * gan: this table shows what i mean when i say that EC is no
      consistency in their definition
    * kamala: [w.r.t. causal consistency] for concurrent operations, there's
      going to be some effector order; does that mean that [the definition in
      the table] also places a constraint on the visibility order?
        * lindsey: [typist couldn't keep up]
    * gan: a consistency scheme is a [typist couldn't keep up]
        * delta [typist couldn't keep up]
        * vis - binary relation on events [typist couldn't keep up]
        * eo - binary relation on events which describes the order
    * lindsey,kamala,gan: [discussion of RedBlue consistency]
    * patrick: reminded me of the observation in the paper that there's a continuum
      between putting complexity into the environment (consistency scheme) and
      having simpler crdts, or putting complexity into the CRDTs and allowing the
      environment to be less constrained
        * lindsey: yeah, the tradeoff is between complexity of the data
          structure, the space the data structure must take up, etc.. and in
          complexity, you get that it works under the weaker consistency models
* lindsey: a question i had was .. what is the relationship between PSI
  Parallel Snapshot Isolation and SC Sequential Consistency.. [typist couldn't
  keep up]
    * sohum: it seems like PSI is a generalization of RB, where there are more
      than just two consistent sets [typist couldn't keep up]
    * lindsey: redblue just means there are two consistency models
    * lindsey: in their PSI+RB where red means PSI and blue means EC
        * "psi for some operations, and ec for the rest of them"
        * if somebody specifies redblue consistency, that tells you nothing,
          until they specify what consistency models are being combined, and
          which operations are which
* gan: going back to the question of "what this paper is about" ... this paper
  is about the parameterized verifications of crdts ... and the second part is
  about making the previous part automated
    * gan: section 3 is about the parameterization; they did this by defining
      consistency schemes as logic formulas
    * gan: the next section is about how they automated things
    * gan: i spent my time in section 3, to understand the parameterization of
      consistency schemes for CRDTs
    * lindsey: this is the first technique for verifying CRDTs which is both
      automated and parametric on consistency policies
    * lindsey: and a valuable result is that causal consistency is neither
      necessary nor sufficient for all of these CRDTs to converge
    * lindsey: then the automation is cool, because they came up with this
      condition, which they call NIC, which even though it takes a lot of
      painstaking work to prove that NIC implies SEC, nevertheless, NIC is
      machine checkable
    * lindsey: i think that this is very nice, and this paper has not been
      widely read or understood yet, perhaps because it was published in a
      different community
* patrick: what is cav?
    * lindsey: computer aided verification, they use a lot of solvers,
      sometimes they verify hardware
    * lindsey: there's a lot of PL overlap because in the last 10 years there's
      been an increase in the use of solvers in PL
    * lindsey: because they sent the paper to CAV and not to a PL conference,
      they wrote it in this way
* sohum: can we discuss noninterference of commutativity (NIC)?
    [displaying Definition 7]
    * lindsey: i find this ugly, because we have to talk specifically about
      executions that have 2 events and that have 3 events
    * lindsey: this first part has to do with executions with 2 events
    * lindsey: this next one they stick a third event in the beginning, and if
      i understand correctly, they are trying to, because they want to talk
      about executions of arbitrary length, [typist couldn't keep up; they want
      to make an indictive proof]
    * patrick: what's weird here?
        * lindsey: the third event at the beginning ...
        * sohum: maybe this is easier to machine-check than the standard
          induction formulation?
        * lindsey: [that could be it]
    * patrick: can it be summarized as "events are commutative"?
    * kamala: is it like "it doesn't matter what your starting state is?"
        * lindsey: it does matter
* lindsey: [glance at the structure of the proof; typist didn't take notes
  here]
    * lindsey: i think the hard thing to understand is how their model, this
      transition system, relates to distributed systems
* kamala: they mention somewhere that they talk about the start state and [two
  operations that must commute]
    * lindsey: there's something, in section 3
    * paper: "we restrict the start state of all events in a well-formed
      execution to be the initial CRDT state sigma_init"
    * lindsey: the key for me to understand this is that every event is an
      entire history, and the events can all be thought of starting in the
      initial state of the CRDT, and there isn't a separate notion of state
      anywhere
    * gan: this relates to their alternative definition of effectors/operations
      given at the beginning of section 3
* lindsey: [discussion of effector definitions in section 2 and section 3]
* lindsey: they don't link up their discussion with what clients can and cannot
  observe, that lookup operation on simple-set is not the same signature of the
  effectors and isn't discussed..
* [discussion of the language used for implementing CRDTs]
    * gan: <https://github.com/Kartiknagar/CAV19-Encodings>
    * gan: i think it's a standard language
* patrick: is the table with consistency schemes a standard notation called
  EPR?
    * lindsey: no, look at the repo for EPR
    * lindsey: it's pretty normal that papers will use a summary notation which
      is mostly conventional across papers in figures and text
    * see also run your research <https://digitalcommons.calpoly.edu/cgi/viewcontent.cgi?article=1236&context=csse_fac>
* lindsey: [summary] first technique for proving SEC for CRDTS that is
  parametric on consistency policies and it's an implementation of those
  policies, and it's a rigorous understanding of this relationship between
  commutativity ... [typist couldn't keep up]

# 2020-07-03, paper discussion

## Week 2 of 2 for [Verifying Strong Eventual Consistency in Distributed Systems](https://dl.acm.org/doi/10.1145/3133933) by Gomes et al. (OOPSLA '17)

* lindsey: since we have fewer people, we don't need breakout groups this time
* lindsey: let's start with the sections we didn't get to last time, such as
  section 5
    * lindsey: for structure, here are the questions again:
        2. What's one thing I learned?
        3. What's one question I'm curious about?
        4. What's one step I can take towards answering the question? 
    * lindsey: can anyone answer question 2?
        * gan: i liked how they formalized the system [lindsey: starting in
          section 4? gan: yes]
        * gan: a system has a state, and an interpret function :: input ->
          state -> state
        * patrick: i like the way they prove commutative, with a reordering of
          composition of klesli composition
        * kamala: [typist couldn't keep up]
    * kamala: it seems that distinctness of message ids in the network is
      required to prove that RGA operations commute
        * sec 6.2: "it is straightforward to demonstrate that delete..."
        * lindsey: [describing distinction between RGA and OT]
            * ot transforms the indexes specified in operations
            * RGA requires unique identifiers on all elements
        * patrick: [comment about UMD paper using UUIDs on causal tree]
            * lindley: i think the author of causal tree said that it's the
              same as RGA
        * lindsey: is kamala's question about message IDs or element IDs?
            * kamala: [typist couldn't keep up]
            * lindsey: if you want to refer to messages, you end up needing a
              unique identifier
            * lindsey: in the RGA section they say RGA requires the total order
              of IDs to be consistent with causality, and they're using Lamport
              clocks rather than vector clocks 
                * patrick: i think that's a flattened version of causal tree,
                  where in causal tree you're pointing toward the thing that
                  comes causally after
            * lindsey: the RGA paper is comprehensive, moreso than the original
              CRDT paper .. it's difficult to demonstrate that RGA is a CRDT
            * lindsey,patrick: [typist couldn't keep up]
            * lindsey: the formal definition of CRDTs is neither necessary nor
              sufficient for people to get work done
* lindsey: the shorter, more dense, paper we're reading next week will shed
  light on CRDTs; causal delivery is neither necessary nor sufficient for CRDTs
  in general; when do you need it and when do you need something else?
* kamala: coming back to the delete-commute thing: i think that the reason i
  made this note is because
    * kamala: when you delete an element it has to be unique; you don't have a
      a number of inserts on the same element, you can only have one
    * lindsey: it also only delete's the first element which matches, so there
      better be only one
* farhad: i've been reasoning about operations on a shared state accessed by
  different processes, and how/when they're compatible with eachother
    * farhad: i've made some progress by paying more attention to the network
      model
    * farhad: [typist couldn't keep up]
    * lindsey: none of the CRDTs here map completely onto what you're trying to do

* lindsey: patrick do you have a question "3. What's one question I'm curious
  about?"
    * patrick: cocurrent-ops-commute question
* lindsey: there's a section where they say "additional requirements regarding
  the contents of messages that cannot be expressed in isabelle's type system"
* gan: in section 8, they mention that "to make a system converge you can use
  last write wins"
    * [typist couldn't keep up]
    * lindsey: cassandra used plain old timestamps in 2013, which lead to
      obvious problems
    * lindsey: let's assume those clocks agree to a high resolution
    * lindsey: cassandra was also rounding clock values from milliseconds to
      hundredths of a second, leading to more time conflicts
    * lindsey: then, if the operations conflict, it just picked the
      lexicographically higher value
    * lindsey: so that's why we need to have some notion of what users want, in
      the form of a guarantee that writes don't get lost
[rest of discussion was not transcribed]

# 2020-06-26, paper discussion

## Week 1 of 2 for [Verifying Strong Eventual Consistency in Distributed Systems](https://dl.acm.org/doi/10.1145/3133933) by Gomes et al. (OOPSLA '17)

* lindsey: goal of the summer reading group is to read papers relevant to CASL,
  consistency aware solvers and languages
    * the name has connotations of security, trustworthiness, and beauty
    * the letters can be re-purposed easily (coordination avoiding.. etc) so the
      acronym is reusable
    * we are interested broadly in programming language level ways of (ensuring
      safety guarantees)
    * we decided to read this paper first about strong eventual consistency:
      [Verifying Strong Eventual Consistency in Distributed
      Systems](https://dl.acm.org/doi/10.1145/3133933) by Gomes et al. (OOPSLA
      '17)
    * the CRDT people introduced the term Strong Eventual Consistency (SEC)
    * This paper formally verifies SEC for some CRDTs
    * This paper is special because it treats the network as a first-class
      citizen
    * One problem with much pl research is that the network is (shrugged off or
      assumed out of the proofs)
        * This paper explicitly models the network, and makes assumptions
          explicit, and bakes those things into the verification
        * You could argue with those choices about what assumptions, but at
          least they're there!
* lindsey: we thought we'd do this paper reading group for 10 weeks
    * it should be student driven, so we can take more than one meeting/week to
      discuss an interesting paper
    * we have a github org https://github.com/lsd-ucsc/CASL
    * here is the reading tentative schedule
      https://github.com/lsd-ucsc/CASL/blob/master/reading-group-schedule.md
    * (lindsey went over the schedule, typist couldn't keep up)
* lindsey: sometimes having a discussion is easier in smaller groups
    * thinking ahead to teaching in the fall, having breakout rooms might make
      discussion easier
    * we could do 30 minute discussions in smaller groups and then reconvene in
      the bigger group?
    * here are some questions to guide discussion
        1. What's the paper about?
        2. What's one thing I learned?
        3. What's one question I'm curious about?
        4. What's one step I can take towards answering the question? 
* kamala: this isn't my area and i haven't read the whole paper
    * lindsey: that's ok! i hope we're all (paraphrased: here to discuss it!)
* lindsey: (creating breakout rooms)
    * [assigning 9 participants into 3 rooms]

[breakout room group]

* we came up with a few questions, then lindsey joined
    * farhad: "is causal consistency stronger or weaker than strong eventual
      consistency?"
    * sohum: "the paper notes that it is sufficient to require that concurrent
      operations commute, even in the presence of operations that can fail. why
      is that sufficient?" (end of section 4.3)
        * lindsey was also curious about that section noted at end of 4.3
* lindsey: this paper has a diagram that describes the proof, and also
  describes the paper flow (something to aspire to)

[back to main discussion group]

* lindsey: this is test run for group discussions in the fall classes; thinking
  about roles in the breakout (eg. recorder and speaker for a group)
    * lindsey: this group is more relaxed, but can somebody from each
      breakout group volunteer?
* kamala:
    * kamala: first we talked about .. (typist couldn't keep up)
    * kamala: then we talked about convergence, and what that means .. (typist
      couldn't keep up)
    * kamala: for me, knowing how the high-level properties are defined as
      "correct" will help to match with the proofs and understand them
        * gan: (with a question related to kamala's question about high level
          definiions) is eventualy consistency the same as "no consistency"?
        * lindsey: i think eventual consistency doesn't belong in the same
          hierarchy as the other consistency models; it's a liveness property
        * lindsey: this paper discusses two safety properties (consistency and
          progress), but there's nothing in here about liveness
        * lindsey: i think kamala raises a good point, which is that normally
          the whole reason we do this is to have better avalability. that's the
          only reason to sacrifice anything other than (linearizability)
        * lindsey: (typist couldn't keep up) .. given that those bad things
          (about the network) that could happen, and this replication algorithm
          or data structure, what kinds of guarantees do we still have? ..it
          depends..
        * gan: my take (on eventual consistency) is that it says "eventually
          things will converge" but that "eventually" is unbounded
        * gan: but in the paper, they provide a definition (typist couldn't
          keep up) which discusses the sets of messages recieved
        * (patrick, kamala commented; typist couldn't keep up)
        * lindsey: (typist couldn't keep up) this paper doesn't talk about the
          availability guarantees
* patrick,farhad: "is causal consistency stronger or weaker than strong
  eventual consistency?"
    * lindsey: this paper makes an assumption that causal consistency is baked
      in and isn't discussed
    * lindsey: next week we're going to discuss a paper that shows that causal
      consistency is too strong for some CRDTs and too weak for other CRDTs
    * farhad: it's possible that (inaudible) ..
    * lindsey: right, it could be that the particular definition of strong
      eventual consistency isn't comparable with common definitions of causal
      consistency
    * farhad: my followup question was, what are the differences in the
      guarantees of causal consistency and strong eventual consistency?

* lindsey: ok, we are out of time
    * we haven't discussed this whole paper; we have a choice; we can discuss
      this paper more next time?
    * farhad, kamala, others: let's discuss this paper more next week!

* gan: for the CASL group we should agree on a set of common definitions for
  these concepts!
    * lindsey,others: yes, that's a good idea
