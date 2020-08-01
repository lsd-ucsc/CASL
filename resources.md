This list is massively under construction!

Some links below only work while on UCSC campus or while logged in with a CruzID.

# Big picture

 * Kuper and Alvaro, Toward domain-specific solvers for distributed consistency (SNAPL 2019): https://users.soe.ucsc.edu/~lkuper/papers/consistency-aware-solvers-snapl19.pdf
    - A high-level "vision" paper that describes some of what we want to accomplish with the CASL project.
 * Lindsey's Google FRA research statement (funded! :slightly_smiling_face:): https://ucsc-lsdlabs.slack.com/files/UP4NTL25Q/FUGNP62F7/google-research-statement.pdf

# Background on SAT/SMT solver internals

  * Marijn J. H. Heule and Oliver Kullmann, The science of brute force (CACM 2017): https://m-cacm.acm.org/magazines/2017/8/219606-the-science-of-brute-force/fulltext
    - Good high-level overview of SAT solving and CDCL.
  * Daniel Kroening and Ofer Strichman, Decision Procedures: An Algorithmic Point of View, second edition (2016): https://link-springer-com.oca.ucsc.edu/book/10.1007%2F978-3-662-50497-0
    - Read chapters 1 (for overall background), 2 (for SAT background), and 3 (for SMT background).  The later chapters have to do with specific theories and decision procedures for them.
    - Chapter 10 deals with Nelson-Oppen combination of theories.
  * CDCL SAT chapter from Handbook of Satisfiability: https://ebookcentral-proquest-com.oca.ucsc.edu/lib/ucsc/reader.action?docID=448770&ppg=145
    - An alternative to Kroening and Strichman chapter 2.

# CRDTs and strong eventual consistency

## Background 

  * Marc Shapiro, Nuno Preguiça, Carlos Baquero, and Marek Zawirski: Conflict-free replicated data types (2011 INRIA tech report): https://pages.lip6.fr/Marc.Shapiro/papers/RR-7687.pdf
    - This paper was published in SSS '11 (https://dl.acm.org/doi/10.5555/2050613.2050642), and that's the version that should be cited, but the tech report version is the one that's free online.  It includes discussion of op-based and state-based CRDTs, the equivalence result for the two, assumptions of the underlying network (e.g., causal delivery for op-based CRDTs), and a few examples.
  * Marc Shapiro, Nuno Preguiça, Carlos Baquero, and Marek Zawirski: A comprehensive study of Convergent and Commutative Replicated Data Types: https://hal.inria.fr/inria-00555588/document
    - This is the "companion" tech report to the previous one, and is a more extensive catalog of example CRDTs.

## CRDT verification

  * Verifying Strong Eventual Consistency in Distributed Systems by Gomes et al. (OOPSLA '17)
  * Automated Parameterized Verification of CRDTs by Nagar and Jagannathan (CAV '19)

(See the related work sections of both of the above for more on CRDT verification.)

# Alternatives to using an off-the-shelf/external SAT/SMT solver
  
  * WT Hallahan, A Xue, R Piskac, G2Q: Haskell constraint solving (Haskell Symposium 2019): https://link-springer-com.oca.ucsc.edu/book/10.1007%2F978-3-662-50497-0

# Background on refinement types

  * Michael Greenberg's blog post on definitions of refinement types: http://www.weaselhat.com/2015/03/16/a-refinement-type-by-any-other-name/
    - See comment from Frank Pfenning: what Liquid Haskell calls refinement types is what Frank would call "subset types".

  * The original paper on refinement types (not exactly the same notion of refinement types as what's used in the Liquid Types / Liquid Haskell work): T Freeman, F Pfenning, Refinement types for ML (PLDI 1991): https://www.cs.cmu.edu/~fp/papers/pldi91.pdf

# Liquid Types and Liquid Haskell stuff

  * PM Rondon, M Kawaguci, R Jhala, Liquid types (PLDI 2008): http://goto.ucsd.edu/~pmr/papers/liquid-types-pldi08.pdf
    - The original paper on liquid types.  Implementation in OCaml, on top of an SMT solver, Z3.
  * N Vazou, EL Seidel, R Jhala, D Vytiniotis, Refinement types for Haskell, (ICFP 2014): https://goto.ucsd.edu/~nvazou/refinement_types_for_haskell.pdf
    - Port of Liquid Types to Haskell, where they had to confront laziness.
  * B Cosman, R Jhala, Local refinement typing (ICFP 2017): https://ranjitjhala.github.io/static/local_refinement_typing.pdf
    - Further improvement on Liquid Types type inference

TODO: there are lots more LH papers than this.  Some might have lesser or greater relevance to us.

# More solver-aided languages

## Rosette

  * Emina Torlak and Rastislav Bodik, Growing solver-aided languages with Rosette (Onward! 2013): https://homes.cs.washington.edu/~emina/pubs/rosette.onward13.pdf
    - See also the PLDI '14 paper on Rosette and other papers at https://emina.github.io/rosette/pubs.html, plus tons more follow-up work that uses Rosette.

## Kaplan

  * Ali Sinan Köksal, Viktor Kuncak, and Philippe Suter, Constraints as control (POPL 2012): https://dl-acm-org.oca.ucsc.edu/citation.cfm?id=2103675

## Smten
 
  * Richard Uhler and Nirav Dave, Smten with satisfiability-based search (OOPSLA 2014): https://dl-acm-org.oca.ucsc.edu/citation.cfm?id=2714064.2660208

# Background on causal consistency and mechanisms for implementing it

  * Lamport, of course: https://lamport.azurewebsites.net/pubs/time-clocks.pdf
    - This one, you really only need the concepts of logical clocks and Lamport clocks, and causality from early in the paper.
  * F Mattern, Virtual Time and Global States of Distributed Systems (1989) https://www.vs.inf.ethz.ch/publ/papers/VirtTimeGlobStates.pdf 
    - One of two papers that introduced the concept of vector clocks around the same time (the other was Fidge)
  * Schwartz and Mattern, "Detecting Causal Relationships in Distributed Computations: In Search of the Holy Grail" (1995): https://www.vs.inf.ethz.ch/publ/papers/holygrail.pdf 
    - Later paper that summarizes ideas from previous work on vector clocks, etc., in an easier-to-understand way
  * Mustaque Ahamad et al., "Causal memory: definitions, implementation, and programming" (Distributed Computing, 1995): https://link-springer-com.oca.ucsc.edu/content/pdf/10.1007%2FBF01784241.pdf
  * Ken Birman et al., Lightweight causal and atomic group multicast (TOCS 1991): https://www.cs.cornell.edu/courses/cs614/2003sp/papers/BSS91.pdf
    - Particularly pay attention to the part of section 4 that covers vector clocks and section 5.1 that covers the CBCAST (causal broadcast) protocol.
  * W Lloyd, MJ Freedman, M Kaminsky, Don't settle for eventual: scalable causal consistency for wide-area storage with COPS, http://www.roday.info/pdf/eventual/0.pdf
  * M Perrin, A Mostefaoui, C Jard, Causal consistency: beyond memory, https://arxiv.org/pdf/1603.04199.pdf
    - Follow-up to the causal memory paper that supports more sophisticated abstract data types, not just an array supporting reads and writes.

# Recent work on verifying causal consistency (either with an automated solver, or with a proof assistant):

  * KC Sivaramakrishnan, G Kaki, S Jagannathan, Declarative programming over eventually consistent data stores (PLDI 2015): kcsrk.info/papers/quelea-long.pdf (Quelea)
    - (LK todo: add slides)
    - (LK todo: point to Burckhardt stuff)
  * V Balegas, S Duarte, C Ferreira, R Rodrigues, Putting consistency back into eventual consistency (EuroSys 2015): https://hal.inria.fr/hal-01248191/document
    - This is the Indigo paper.
  * M Lesani, CJ Bell, A Chlipala, Chapar: certified causally consistent distributed key-value stores (POPL '16): https://www.cs.ucr.edu/~lesani/companion/popl16/POPL16.pdf
    - Using a proof assistant (Coq) to verify causal consistency of a key-value store
  * A Bouajjani, C Enea, R Guerraoui, J Hamza, On verifying causal consistency, https://arxiv.org/pdf/1611.00580.pdf
  * ’Cause I’m Strong Enough: Reasoning about Consistency Choices in Distributed Systems: https://pages.lip6.fr/Marc.Shapiro/papers/CISE-POPL-2016.pdf
    * aka CISE

# Custom solvers that resulted in efficiency improvements

These are papers about systems where using an off-the-shelf solver didn't work as well as using a custom one.

  * Guy Katz, Clark Barrett, David Dill, Kyle Julian and Mykel Kochenderfer, Reluplex: An efficient SMT solver for verifying deep neural networks (CAV 2017), https://arxiv.org/pdf/1702.01135.pdf
  * Ge et al., SMT Solving for the Theory of Ordering Constraints (LCPC 2015): https://parasol.tamu.edu/~jeff/academic/coco.pdf
    - Data race prediction in concurrent programs.  They figured out that a theory built into Z3 was less efficient to solve and had things they didn't need.

# What should I read first to understand causal consistency and how to implement it!?

  - The "Holy Grail" paper
  - The causal memory paper
  - sections 4 and 5 of the CBCAST paper
