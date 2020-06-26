These are paraphrased notes from a live discussion with several people.
Parenthesis are used where the paraphrasing is particularly distant from what
the speaker said. Feel free to edit these notes as you see fit.

# 2020-06-26, paper discussion

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
