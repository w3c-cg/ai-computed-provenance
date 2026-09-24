# R2 — Primer: AI Computed Provenance Format

*Draft of the R2 Non-Normative Report, following the Charter draft of 2026-09-23. The
specification this primer introduces, S1, has not yet been written. The primer therefore
describes the concepts the Charter commits the group to, not a finished format. Examples use an
informal notation, not the format's syntax.*

## 1. Introduction

This primer introduces the AI Computed Provenance Format to practitioners in scientific and
technical fields who are not specialists in formal methods. The specifications rest on type
theory, justification logic, and category theory. This document explains, in practical terms,
what the format records and what a reader can do with a record, and it uses worked examples in
place of formal definitions.

The format records how a fact came to be known in an AI-assisted scientific or technical
workflow, in a form that a second party can check without access to the system that produced
it.

This primer is non-normative. Where it mentions a requirement, the requirement is stated in the
Charter or in S1, not here.

## 2. The problem

An AI-assisted pipeline produces a conclusion: a compound inhibits an enzyme, a model change
improves accuracy, a component meets a load bound. The steps that led to the conclusion are
visible only to the system that took them. A reader who wants to check the conclusion has two
options: re-run the pipeline, if it is available, or accept the report. Neither is an audit.
Re-running shows that the pipeline is repeatable, not that its conclusion follows. Accepting the
report checks nothing.

As automated systems produce candidate conclusions faster than people can review them, the
bottleneck moves from producing claims to establishing which claims are sound. The format
addresses that bottleneck by making the basis of each claim part of the record.

## 3. Computed standing, not asserted standing

The format's central principle is that the standing of a claim is computed from the evidence
recorded for it, and never asserted by the party that produced it.

Suppose a system reports "verified: compound K inhibits enzyme E". The word "verified" is a
statement of the same kind as the claim. A reader who doubts the claim has equal reason to doubt
the label, and checking either requires trusting the system.

In this format, no producer writes "verified". A record contains the claim and the evidence for
it. A reader holding the record computes the claim's standing from that evidence, and every
reader holding the same record computes the same standing. The specification forbids an
implementation from letting a producer set a claim's standing directly.

## 4. Two kinds of evidence: proofs and grounds

Recorded evidence is of two kinds.

A **proof** establishes that a statement holds. It can be checked mechanically: anyone with the
proof and a checker for its logic reaches the same verdict, and doing so requires no trust in
whoever produced the proof. A proof that checks settles the question.

A **record of grounds** establishes what a claim rests on: a dataset, an analysis plan, an
assertion by a named person. It does not establish the claim. A reader can check that the
record is well formed, and can see exactly what and whom the claim relies on. A reader who
doubts one of those premises must examine it directly; the record cannot do that on the
reader's behalf.

|                                          | Proof                                      | Record of grounds                                              |
| ---------------------------------------- | ------------------------------------------ | -------------------------------------------------------------- |
| Establishes                              | the statement itself                       | what the claim rests on                                        |
| If it checks, the statement holds        | yes                                        | no                                                             |
| Another party reaches the same verdict   | yes, given a checker for its logic         | only by accepting the same premises                            |
| Typical sources                          | proof assistants; the format's own checker | instruments, datasets, analysis software, AI models, people    |

Most scientific evidence is of the second kind, and that is not a weakness of the format. An
instrument reading or an expert's judgement cannot be proved. It can only be recorded honestly,
with its source named.

The danger lies in confusing the two. If a system keeps grounds and proofs in the same place,
software can easily turn "these are the grounds for P" into "P is proved". The format keeps them
as different kinds of object, and the specification requires that no rule converts one into the
other. A record of grounds can be checked for well-formedness, but that check is a statement
about the grounds, not a proof of the claim.

## 5. Provenance and warrant

The format records two separate properties of a resource, which answer different questions.

|            | Provenance                                                  | Warrant                                                   |
| ---------- | ----------------------------------------------------------- | --------------------------------------------------------- |
| Question   | How did this come to exist?                                 | What evidence supports what it states?                    |
| Applies to | every resource                                              | only resources that state something                       |
| Example    | produced by run 17 of pipeline P, from dataset D, on 3 March | rests on an observation and on a declaration by Dr. S    |

Every resource has provenance. Only a resource that states a proposition has a warrant. A
vocabulary term, a class definition, or an imported concept states nothing that could be true
or false, so asking what proves it is a category error rather than an open question.

The two are independent. A claim typed in by a person and accompanied by a proof that checks is
verified. A claim produced by a sophisticated AI system with no proof is not. Where a claim came
from does not change what supports it.

Provenance is already well served by existing standards. The format maps its provenance records
onto W3C PROV-O, so PROV tooling can read them. PROV deliberately does not model what a resource
says, only where it came from. Warrant concerns exactly what a resource says, and is the part
this group specifies.

## 6. The three grounds

Every justification rests, at bottom, on grounds of three kinds.

| Ground       | What it establishes                                                                              | What a reader must accept to rely on it                                         |
| ------------ | ------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------- |
| **Verified** | the statement itself, by a proof that checks                                                     | the checker, and for a proof from another system, the translation between them |
| **Observed** | that a recording occurred: this instrument, on this sample, under this protocol, gave this output | that the recording is faithful; any wider claim needs a further premise          |
| **Declared** | that a named, accountable party asserted the statement                                           | trust in that party                                                             |

Each ground establishes a specific statement, and that statement is often narrower than the
claim it supports. An assay reading establishes that the assay produced a number, not that the
compound works. A declaration establishes that someone said so, not that it is so. Only a proof
establishes the claim itself.

This gives the format its main rule: **record only what was actually established.** Where a
conclusion goes further than its evidence, the step between them is itself recorded, as a
declared premise attributed to the person or organization accountable for it. The gap is not
hidden inside the conclusion. It is a visible, attributed item that a reader can inspect,
accept, or reject.

Grounds are not trust scores, and the format does not rate sources. A declared ground tells the
reader whom a claim relies on. Whether to rely on that party is the reader's decision.

## 7. How grounds combine

A justification is a structure, not a single label. Grounds combine, and the most common
combination is **application**: a plan applied to an input.

Consider an analysis plan, such as a curve fit, applied to measured data. The conclusion rests
on two grounds:

- a declaration that the plan is a function of its input, meaning that the same data always
  produce the same result; and
- the observation of the input data.

Informally:

```
fitted value  rests on  apply( declared(the plan is a function of its input, by analyst A),
                               observed(plate readings, run 42) )
```

Two consequences follow.

First, whether the fitting software actually ran is provenance, not evidence. If the plan is a
function, the result follows from the data whether or not anyone ran it, and anyone can re-run
it to check. The declaration that the plan is a function carries the whole claim of
reproducibility.

Second, the conclusion is not verified. Its grounds include a declaration: someone asserted that
the plan is a function, and nobody proved it. For software outside the format, whether a program
computes a mathematical function cannot be decided in general, so the assertion is the honest
record.

Where no such declaration can be made, because the process is stochastic, the output is a
sample. The record of the run is itself the evidence, and the result is a bare observation:
this run produced this output. Nothing follows about the next run.

A computation can become verified. If the plan is written in the format's own statement
language, its typing is proved rather than declared, and its input is recorded as data, then
the checker verifies the step itself. The statement verified is specific to that input —
"fitting these readings gives 3.2 µM" — and does not extend to other inputs. The readings
themselves remain an observation.

A claim may also rest on several alternative justifications, each complete in itself. It is
fully verified if every ground in at least one of them is verified.

## 8. Why warrant is computed and not stored

Because warrant is computed from the justification, it cannot contradict the evidence. A stored
label can. A resource marked "verified" with no proof behind it is a state that a system of
stored labels can represent, and must then detect. In this format that state cannot be written
down, so it never needs to be detected.

Computation also keeps warrant current. If analyst A withdraws the declaration that the plan is
a function, every conclusion resting on it loses that support at once, without anyone editing
those conclusions. Records are never modified in place, so the withdrawal is itself a new
entry, and the earlier state of the record remains available and citable. The specification
states which state of the record a warrant is evaluated against.

One rule keeps computed warrants sound: support must not be circular. A claim cannot serve,
directly or through a chain of other claims, as support for itself. The format rejects circular
support when content is written.

Because a justification is a structure, a reader or a tool can ask it questions that a label
cannot answer:

- Is this claim fully verified?
- Which people or organizations does it rely on?
- Which observations does it rest on?
- Does it still stand if a given observation or declaration is removed?
- Which recorded claims depend on a given declaration?

## 9. Worked example: an enzyme inhibition claim

A research group, assisted by an automated pipeline, wants to record the following claim.

> **C:** Compound K inhibits enzyme E with an IC50 below 10 µM.

The pipeline records five items.

| Item | Content                                                                                                                      | Justification            |
| ---- | ---------------------------------------------------------------------------------------------------------------------------- | ------------------------ |
| R1   | Plate-reader outputs: 8 concentrations, 3 replicates each, under assay protocol P                                            | Observed                 |
| R2   | Analysis plan: a four-parameter logistic fit reporting IC50 with a 95% confidence interval; and the plan is a function of its input | Declared, by analyst A   |
| R3   | The fitted IC50 for R1 is 3.2 µM, with a 95% confidence interval of 2.1–4.8 µM                                               | R2 applied to R1         |
| R4   | Bridge: if this assay's fitted IC50 has an upper confidence bound below 10 µM, then K inhibits E with an IC50 below 10 µM     | Declared, by scientist S |
| C    | The claim                                                                                                                    | R4 applied to R3         |

The justification for C, informally:

```
C  rests on  apply( declared(R4, by S),
                   apply( declared(R2 is a function of its input, by A),
                          observed(R1) ) )
```

The provenance of each item — which instrument, which operator, which software version, which
pipeline run — is recorded separately and maps onto PROV. It does not appear in the
justification.

A reviewer holding the record, with no access to the lab or the pipeline, can establish the
following.

- **Is C verified?** No. Its grounds are one observation and two declarations. No proof is
  involved.
- **Whom does C rely on?** Analyst A, for the claim that the fit is reproducible; and scientist
  S, for the step from one assay's result to a claim about the compound.
- **What would re-running the analysis establish?** Re-running R2 on R1 tests A's declaration
  on this input. It does not touch S's bridge or the fidelity of the readings.
- **What does the statistical analysis establish?** R3 is a statement about these readings, at
  the scope the plan declares. The step from R3 to C belongs to S, and it is recorded as S's.
- **What happens if S withdraws R4?** C loses its support. R3 is unaffected.

The record does not say that C is true, and it does not say that C is well supported. It says
exactly what C rests on, and leaves the assessment of those premises to the reader. That is the
audit the pipeline's report alone could not provide.

Suppose the group later writes the fit in the format's statement language, with its typing
proved. R3 then becomes a verified statement about these readings, and A's declaration is no
longer needed. R1 remains an observation, and R4 remains S's declaration. The standing of C
improves by exactly the evidence that changed, and no more.

## 10. AI model outputs are observations

A call to a generative model is a recording made under a declared protocol, just as a
laboratory assay is. What decides how the output is recorded is not the medium but whether the
process can be declared a function of its input.

For a model invocation, determinism is an empirical property of the environment, not a property
of the code. "Temperature zero with a fixed seed" is an assertion by whoever configured the run;
it cannot be derived from a configuration file. A model output is therefore recorded as an
observation: this run, with these inputs, produced this output.

This has a direct consequence for claims about AI systems. "Reranker R improves answer accuracy"
is a general claim, and a single evaluation run supports it no better than a single animal
supports a claim that a drug is effective. The same statistical apparatus applies: repeated
runs, a declared analysis plan, a declared scope, effect sizes, and a declared bridge from
"these runs" to "the system". The format records each of those steps with its ground and its
accountable party.

## 11. Checking statements

A knowledge graph can record that a resource has a property. It cannot, by itself, record a
proposition such as "this compound inhibits this enzyme below a stated concentration", which has
internal structure, quantifiers, and a condition under which it is true.

The format therefore stores statements in a precise statement language, based on dependent type
theory, as ordinary content in the record. Each statement is checked when it is written: that
it is well formed, and that it uses the vocabulary in force correctly. A statement that fails
the check is not admitted.

The unit that is checked is a **judgement**: a term, the type it is claimed to have, and the
logic in which the claim is made. When the type is a proposition and the term is a proof of it,
a checked judgement is a verified proof. Naming the logic lets the same form record proofs
checked by the format's own checker and proofs from other systems, such as a proof assistant,
for which a checker is available.

The statement language defines what a statement is. It does not decide whether a statement is
true; that is the role of evidence.

## 12. The record

Records form a typed knowledge graph, and the format builds on existing Web standards: RDF and
JSON-LD for serialization, and SHACL for shape constraints.

- **Immutable layers.** A record grows by adding layers. Content is never modified in place, so
  a citation to a fact is a citation to a specific state of the record.
- **Content addressing.** Each layer is identified by a cryptographic hash of its content. The
  specification requires two parties to compute the same identifier for the same content,
  which means fixing a canonical encoding. The choice of encoding, for example the JSON
  Canonicalization Scheme or deterministic CBOR, is an early work item and is not yet settled.
- **Validation when content is written.** A fixed, enumerable set of structural checks applies
  before content is admitted. A conforming record is one that has passed a stated set of
  checks.
- **Self-contained vocabulary.** Class and property declarations live in the same record as the
  data that uses them, so a change to external configuration cannot silently change what a
  recorded fact means.
- **Imported vocabularies.** Existing vocabularies, from lexical resources such as WordNet to
  licensed ones such as the UMLS Metathesaurus, are imported rather than replaced. An imported
  vocabulary is recorded as an assertion by its publisher, and its licence terms travel with the
  record.

## 13. Several reasoning systems

Scientific conclusions often combine results from systems that work in different logics: a
statistical test, a numerical solver, a proof assistant. These systems share no common notion of
what a statement is or what makes it true, and a format that adopted any one of them would
exclude the others. The format uses the theory of institutions, a mathematical account of what
"a logic" is, so that several reasoning systems can participate without any one of them serving
as the standard.

A participating reasoning system declares its vocabulary, the questions it answers, and the
verdicts it returns. What it can contribute depends on what a reader can check.

- A system that supplies proof objects, for which a checker is available, can contribute
  verified statements. A proof assistant whose proofs the reader can re-check is the typical
  example.
- A system that returns only a verdict cannot. "The test holds at p < 0.05" is evidence recorded
  with its source, not a proof. A statistical system contributes observations and applications
  of declared plans, never verified claims.
- Any reasoning system may refuse content on its own authority, because a wrongful refusal loses
  data but does not corrupt the record. No reasoning system may make a claim verified on its own
  authority.

A reader who accepts a verified claim relies on a small, stated set of components: the format's
own checker, any external checker used, any translation between systems, and the rules under
which declarations and observations are admitted. The specification states that set. It does
not include the software that found a proof, or any system's unchecked verdict.

## 14. What the format does not yet guarantee

The group's first specification covers a single producer working in a single context. The
Charter requires the specification to state where its guarantees stop, and a reader should know
those limits as well.

- **Translation between reasoning systems.** The format records which translation related a
  statement in one system to a statement in another, and checks that the translation is properly
  declared. It does not yet establish that the translated statement means the same as the
  original. A proof from a proof assistant carries across only as far as that translation. This
  is an open research question.
- **Transport between contexts.** A proof in the format's own logic can be re-checked anywhere.
  A record of grounds cannot: a reader in another context must examine its premises afresh.
- **Changes of vocabulary.** A statement is checked against the vocabulary in force where it
  was written. If that vocabulary later changes, for instance when records are combined, the
  first specification does not establish that the statement still means what it meant.
- **Reliability of sources.** The format records whom a claim relies on. It does not score or
  rank sources.

## 15. Combining independently produced records

When several parties extend a shared record independently, their work must eventually be
combined. Combination is not part of the first specification. A single agent working against a
single record never needs it, and the format is useful without it.

The group expects to address combination in two later stages. The first concerns structure:
which contributions survive, how conflicts are classified and resolved, and whether the combined
record passes the same validation its inputs passed. The mathematical model is a pushout, which
describes the combination of two records that diverged from a common ancestor. The second stage
concerns meaning: when combination changes the vocabulary an existing statement refers to, what
must be established for that statement to still hold. That question is open.

## 16. Terms used in this primer

The Charter's glossary defines the formal terms. The following are the terms this primer relies
on.

- **Application:** A combination of grounds in which a plan, declared to be a function, is
  applied to an input. The result rests on both the declaration and the input.
- **Declared:** The ground recording that a named, accountable party asserted a statement.
- **Judgement:** A checked statement that a term has a given type in a named logic. When the type
  is a proposition, a checked judgement is a verified proof.
- **Justification:** The structure recording what a claim rests on and how its grounds combine.
- **Observed:** The ground recording that a recording occurred: an instrument reading, a sample,
  a model output.
- **Proof:** Evidence that establishes a statement and that anyone with a checker can re-check.
- **Provenance:** The record of how a resource came to exist. It maps onto W3C PROV.
- **Record of grounds:** Evidence that records what a claim rests on without establishing the
  claim.
- **Standing:** What a claim's evidence amounts to, as computed from its justification.
- **Verified:** The ground recording that a proof of the statement itself has been checked.
- **Warrant:** The standing of a claim that states a proposition, computed from its
  justification. It is never stored and never asserted.

## 17. Further reading

- The AI Computed Provenance Community Group Charter.
- Sergei Artemov and Melvin Fitting. *Justification Logic: Reasoning with Reasons*. Cambridge
  University Press, 2019.
- Joseph A. Goguen and Rod M. Burstall. Institutions: abstract model theory for specification
  and programming. *Journal of the ACM*, 39(1):95–146, 1992.
- W3C. *PROV-O: The PROV Ontology*. W3C Recommendation, 2013.
  <https://www.w3.org/TR/prov-o/>
