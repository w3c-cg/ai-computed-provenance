# AI Computed Provenance Community Group Charter

*This Charter is a draft under development. To submit feedback, please use Issues in the
repository where the Charter is being developed. Remove this paragraph before submitting the
Charter for approval.*

* **This Charter:** {TBD: URI}
* **Previous Charter:** None. This is the first Charter for this Community Group.
* **Start Date:** {TBD: YYYY-MM-DD}
* **Last Modified:** 2026-09-23

---

## Goals

The mission of this group is to build an open trust layer for AI-driven science and
technology, stewarded as a public good. The current paradigm of AI-assisted research often
requires users to place blind faith in proprietary black boxes. This group standardizes a
paradigm shift: a machine-checked and cryptographically founded foundation that records
exactly how every fact is known.

With that record, auditing a conclusion changes from an act of trust into a process a reader
carries out independently: re-checking the proof where one exists, and otherwise seeing
exactly which premises, and whose, the conclusion rests on. The group operates
on the core distinction that "Computed ≠ Asserted" — the standing of a claim is computed from
the evidence recorded for it, and never asserted by the party that produced it. Because you
cannot outsource "is this true?" to a proprietary black box and have the answer carry
independent weight, this verification layer must remain fundamentally neutral and immune to
single-vendor enclosure.

### The problem the group addresses

AI-assisted research produces conclusions whose derivation is visible only to the system that
produced them. A reader who wants to check such a conclusion has two options: re-run the
producing pipeline, where that pipeline is available, or accept the report. Neither option is
an audit. As automated systems generate candidate conclusions faster than review processes
absorb them, the limiting factor moves from producing claims to establishing which claims are
sound.

### Computed and asserted

The group works from a distinction between two ways a claim acquires standing.

A system that **asserts** the standing of a result — that it was verified, checked, or
computed correctly — makes a statement of the same kind as the result itself. A reader who
doubts the result has equal reason to doubt the assertion, and checking either requires
trusting the producer.

A system that **computes** the standing of a result derives it from evidence held in the
record. A second party holding the same record computes the same standing, and doing so does
not require trusting the producer.

Recorded evidence is of two kinds, and the difference between them matters as much as the
first distinction.

A **proof** establishes that a proposition holds. A second party holding the proof and a
checker for its logic reaches the same verdict, without trusting the producer.

A **record of grounds** establishes what a claim rests on: a dataset, an analysis plan, an
assertion by an accountable party. It does not establish the claim. A second party can check
that the record is well formed and can see exactly whom and what the claim relies on. A reader
who doubts one of those premises must examine it directly; the record cannot do that on the
reader's behalf.

The specifications this group develops define records in which standing is computed rather
than asserted, and in which a record of grounds cannot stand in for a proof. Every other
objective in this Charter follows from these two distinctions.

### Neutrality

Neutrality is a technical requirement of the work rather than a preference of its
participants. If a single party controls the verification layer, then the question "is this
result sound?" resolves to "does that party say so?", and the independence that motivated
verification is lost. This is the reason the group develops an open specification rather than
documenting a product.

W3C and IETF are established models for infrastructure that many parties depend on and no
single party owns.

### Objectives

The group has three objectives.

1. **Define a data format** in which the way a fact is known forms part of the record, can be
   checked by machine, and is independent of the tool that produced it. In this format a
   proof and a record of grounds are different kinds of object.
2. **Enable verification by a second party.** Any party holding a record and the relevant
   specification can re-check every proof the record contains, given a checker for the proof's
   logic, and can re-derive the standing of every claim it records. No reader holds a
   privileged position.
3. **Support federation without a central authority.** The format accommodates multiple
   reasoning systems and multiple vocabularies, with no single party owning the vocabulary.

The group does not define an implementation, a reasoning engine, or a method for scoring the
trustworthiness of sources. The group defines what a record must contain.

### Success criteria

The group considers its first phase of work successful when all of the following hold.

* At least one party other than the maintainers of the existing reference implementation has
  implemented the specification.
* The conformance test suite has been run by an implementer who did not write it, and has
  identified at least one non-conformance.
* At least one published result has been audited from end to end by a reader with no access
  to the system that produced it.
* The group has established a liaison relationship with at least one adjacent group.

---

## Scope of Work

The group develops specifications for recording, and independently checking, how a fact came
to be known in an AI-assisted scientific or technical workflow.

### Use cases

Two use cases define the scope.

**A single agent producing auditable work.** One automated system works against one record
and proposes conclusions. Each conclusion carries a record of how it was produced and a
machine-checkable account of what it rests on. A reviewer holding the record and the
specification, without access to the producing system, re-checks every proof in that account
and re-derives the conclusion's standing: which parts are proved, which rest on observations,
and which rest on declarations by named parties.

**A conclusion drawn across several reasoning systems.** A statistical result, a numerical
bound, and a machine-checked proof each contribute to one conclusion. The record states which
system established which proposition, under which translation, and which declared premise,
attributed to a named party, carries each of those propositions to the conclusion. A
statistical test establishes a statement about a sample at a declared scope, not the domain
claim it supports, and the record keeps the two apart.

A third use case, in which several parties extend a shared corpus independently and then
combine the results, falls within the group's interest but outside the first phase of work.
See [Phases of work](#phases-of-work).

### Concepts the specifications build on

The specifications draw on the following concepts. For each, this Charter states what the
group defines and what the group adopts by reference. Each concept is annotated with the
phase in which the group addresses it.

#### The knowledge graph foundation — Phase 1

Records consist of typed resources with class and property declarations. Vocabulary
declarations reside in the same record as the instances that use them, rather than in
external configuration.

Records accumulate as layers. Each layer is identified by a cryptographic hash of its
content, and content is never modified in place, so a citation to a fact is a citation to a
specific state.

A fixed set of structural validation rules applies before content is admitted to a record.
Validation forms part of the format: a conforming record is one that has passed a stated set
of checks, and those checks are enumerable.

The format supports importing existing vocabularies rather than replacing them. The group
defines how an imported vocabulary is represented, referenced, and attributed. Two classes of
import illustrate the requirements:

* **Lexical resources**, such as WordNet. These supply the vocabulary needed to relate prose
  to structured statements. An imported lexical resource whose terms carry no provenance
  reintroduces an unchecked layer into an otherwise checkable record.
* **Licensed vocabularies**, such as the UMLS Metathesaurus. These are not in the public
  domain. A record containing imported terms MUST carry the licence notice of the source, and
  redistribution constraints MUST apply to every downstream recipient. Where a source
  distinguishes restriction levels among its constituent vocabularies, an importer MUST honour
  those levels.

Four requirements generalize from these cases, and the specification addresses each:

1. A record MUST distinguish a faithful representation of an imported source from any content
   derived from it.
2. Imported structure SHOULD map onto the format's native constructs rather than parallel,
   source-specific constructs, so that imported and native terms are queryable together.
   Identity SHOULD derive from the identifier the source itself assigns.
3. Import MUST be deterministic. Two parties importing the same release of a source produce
   the same content, and therefore the same identifier.
4. Terms of use, source restrictions, and publisher identity form part of the record rather
   than of deployment configuration.

The group also addresses vocabularies whose entries denote individuals rather than kinds,
because the two map differently.

Two problems remain open and the specification states them rather than resolving them
silently: **identity**, meaning the conditions under which an imported class is the same as a
local one; and **trust**, meaning that an imported vocabulary is an assertion by its
publisher and the record states it as such.

The group adopts RDF, JSON-LD, and SHACL by reference for serialization and shape
constraints. The group does not define domain vocabularies.

#### Deterministic encoding and content addressing — Phase 1

Two parties MUST compute the same identifier for the same content. Without this property, no
other guarantee in the format composes.

The group defines the canonical form, the hash function, the extent of content the hash
covers, and the procedure by which that extent changes without invalidating existing records.

Candidate bases include RFC 8785, the JSON Canonicalization Scheme, and Section 4.2 of
RFC 8949, the core deterministic encoding for CBOR. These are not interchangeable, and the
choice affects streaming, numeric representation, and available tooling. Selecting between
them is an early work item rather than a settled decision.

#### A statement language — Phase 1 for checking a statement in the context where it is written; Phase 2 for what survives a change of vocabulary

A knowledge graph can record that a resource has a property. It cannot, by itself, record
that a **proposition** is well formed, or state what would establish it. A statement such as
"this compound inhibits this enzyme below a stated concentration" has internal structure,
quantification, and a truth condition, and is not expressible as a single property
assignment.

The format therefore carries propositions as terms of a dependent type theory, stored as
ordinary content within the record and checked when written.

The unit that is checked is a **judgement**: a logic, a term, and a type, stating that the
term inhabits the type in that logic. A bare term cannot always be checked, because its type
cannot always be inferred from it. A judgement supplies the type, so every check runs against a
stated type and no part of the record needs an exception. Naming the logic lets one form cover
both the format's native theory and any external logic for which a checker is available.

The specification defines the term language, its encoding, the judgement form, the typing
rules a conforming checker implements, and the point in the write path at which checking is
REQUIRED.

The group does not define a reasoning engine. The statement language establishes what a
statement is; it does not establish how any party decides whether that statement holds.

#### Provenance and warrant — Phase 1

The format records two properties of a resource, and they answer different questions.
**Provenance** records how a resource came to exist: what produced it, from what inputs, and
by which activity. Every resource has provenance. **Warrant** records what evidence exists for
the proposition a resource carries. Only a resource that carries a proposition has a warrant.
For a vocabulary term or an imported concept, asking what proves it is a category error rather
than an open question.

The two are independent. A claim written by hand and accompanied by a checked proof is
verified. A claim generated by a machine and carrying no proof is not. The origin of a claim
does not change its warrant.

W3C PROV standardizes provenance. The group maps the format's provenance records onto PROV-O
rather than defining a competing model, and the specification defines that mapping. PROV
deliberately treats an entity as opaque and does not model what the entity states. Warrant is
a statement about exactly that content, so PROV cannot carry it. Warrant is what this group
defines.

#### Justification — Phase 1 for justification terms and the computation of warrant within one context; Phase 2 for transport across a context boundary

The format represents justification following the approach of justification logic, in which
"it is known that F" is replaced by "t is a justification for F". The justification is an
object within the language rather than metadata attached to it. A record carrying a
justification differs in kind from a record carrying only the statement, and the difference
is checkable.

The format keeps proofs and records of grounds in separate layers. A proof is a judgement
whose type is the proposition itself. A record of grounds is a justification term for the
proposition, and it does not assert the proposition. A checker can verify that a justification
term is well formed and grounds a claim to P, but that result is a judgement about the
grounds, not about P. The specification MUST make it inexpressible, and not merely
non-conforming, for a record of grounds to stand in for a proof of the proposition it grounds.

The leaves of a justification term are drawn from three grounds.

* **Verified.** A checked judgement establishes the proposition itself.
* **Observed.** A recording occurred: an instrument reading, a sample, the output of a model
  run. What a recording establishes is narrower than the claims it supports. A party that
  draws the wider claim supplies the premise that bridges the two.
* **Declared.** A named, accountable party asserted the proposition. This establishes that
  the assertion was made. Relying on it means trusting that party.

Justification terms combine these leaves, for example by applying one justification to
another. A conclusion obtained by applying an analysis plan to observed input rests on two
grounds: a declaration that the plan denotes a function of its input, and the observation of
that input. Such a conclusion is not verified, because the typing of the plan is declared
rather than proved. The format records only the proposition that was actually established.
Where the recorded conclusion is wider, the step between the two is a declared premise
attributed to a party.

The **warrant** of a claim is computed from its justification term and the evidence the term
cites. It is not stored as a field and it is not supplied by the producer. A conforming
implementation MUST NOT permit a producer to assert a warrant directly. A self-declared claim
of verification is the practice these specifications are intended to make unnecessary.
Because warrant is computed, it follows the record: when a declared premise is withdrawn,
every conclusion resting on it loses that support without being edited. The specification
states the record state against which a warrant is evaluated.

A justification MUST be well founded: the support of a premise MUST NOT include that premise.
This is a structural validation rule, checked when content is written.

Execution leaves a record of what was run, and that record is provenance. Whether it is also
evidence depends on the process. Where a plan is declared to be a function, its output
follows from its input and the run adds nothing to the warrant. Where the process is
stochastic, as with a model invocation or a laboratory assay, the recorded output is itself an
observation.

The group defines the judgement form, the justification term language, the three grounds and
the rules computing warrant from them, the well-foundedness condition, and the structure of
the records that witness each ground.

#### Federated reasoning — Phase 1 for declaration surfaces; Phase 2 for translation between systems

Different domains use different logics. A statistical test, a numerical solver, and a proof
assistant do not share a common notion of what a statement is or what makes one true. A
format that adopts any one of these excludes the others.

The group therefore follows the theory of institutions, which formalizes the notion of "a
logic" precisely enough that two logics can be related without either serving as the
standard. An institution comprises a category of signatures, the statements available over
each signature, the models of each signature, and a satisfaction relation that is invariant
under change of notation. A comorphism relates two institutions. The Grothendieck
construction assembles a family of institutions into a single surface to which a query can be
dispatched.

The group defines what a conforming reasoning system declares: its signature vocabulary, the
shape of the queries it answers, and the verdicts it returns. The group also defines what a
translation between two reasoning systems declares in order to be admissible.

A reasoning system's authority is limited by what a reader can check. A system that supplies
proof objects, for which a checker is available, can contribute verified claims. A system that
returns only a verdict cannot. A verdict that a statement holds is evidence recorded together
with its source, not a proof, and a claim resting on it is not verified. A reasoning system MAY
refuse content on its own authority, because a wrongful refusal loses data rather than
corrupting it. It MUST NOT establish a verified claim on its own authority. Whether a
reasoning system can contribute verified claims is therefore an operational question: whether
a reader can hold and re-check what it produced, not whether its logic meets a formal
definition.

A specification that defines how a claim becomes verified MUST state what a reader trusts in
accepting it: the native checker, each external checker, each translation between reasoning
systems, and the rules under which declarations and observations are admitted.

The group notes a boundary. Defining a **declaration** surface is achievable in the first
phase. Defining a **statement translation**, over which the satisfaction relation is stated,
is not; see [Open questions](#open-questions).

#### Combining independently produced records — Phase 1.5 for structural validity; Phase 2 for preservation of meaning

Records are not static. Two parties may extend a shared corpus independently, and the results
require combination. This does not apply to the single-agent use case: an agent working
against one record appends to it and never combines, which is why the format is useful before
the group specifies this concept.

The format represents combination over a typed graph, modelled as a pushout of the span that
two divergent records form over their common ancestor.

Combination differs from ordinary version control in one respect that matters here: combining
records can change the vocabulary that an existing statement refers to. Whether that statement
still means what it meant is the question the format makes answerable.

In Phase 1.5 the group defines the taxonomy of conflicts, the available resolutions, and the
requirement that a combined record satisfies the same validation its inputs satisfied.

### Phases of work

The group works in three phases with defined boundaries between them. This Charter covers
Phase 1. Later phases require an amendment; see
[Amendments to This Charter](#amendments-to-this-charter).

Two questions determine the phase to which any work item belongs. First: how many parties
produced the record? Second: must the guarantee survive a change of context?

**Phase 1 — one producer, one context.** The representation, its canonical form and
identifiers, structural validation, the statement language and its judgements checked against
the vocabulary in force where a statement is written, provenance and its mapping to PROV-O,
justification terms and the computation of warrant from them, and the declaration surfaces
for reasoning systems and translations between them.

Phase 1 is a complete deliverable in itself. A single agent working against a single record
never combines records with another party. It produces conclusions, each carrying a record of
how it was produced and a machine-checkable account of what it rests on. This addresses the
distinction between computed and asserted standing for the most common case, and it does not
depend on the later phases.

**Phase 1.5 — several producers, unchanged meaning.** Combining independently produced
records. This work is structural: it concerns which contributions survive combination and
whether the result is well formed, rather than what the surviving statements mean. It is
separate from Phase 1 because implementations obtain value from the format without it, and
separate from Phase 2 because it depends on no unresolved theory.

**Phase 2 — claims that cross a context boundary.** What a combination must establish when it
changes the vocabulary an existing statement refers to; and what a translation between
reasoning systems must carry for a statement established in one system to hold in another.
Both depend on open questions, stated below, and this Charter does not schedule them.

Phases 1.5 and 2 both concern combination, and the boundary between them is as follows.
Phase 1.5 establishes whether the combined **record** is well formed. Phase 2 establishes
whether the combined **claims** still hold.

### Conditional guarantees

Where a guarantee stops at a context boundary, a specification produced under this Charter
MUST state that limit in normative text. The specification MUST NOT leave a reader to infer
that a guarantee extends further than the group has established. Three cases arise:

* A statement is checked against the vocabulary in force where it was written. Neither
  Phase 1 nor Phase 1.5 establishes that the statement remains well typed after records are
  combined, or that its terms denote the same things.
* A declared translation between reasoning systems is well formed. Phase 1 does not establish
  that what a receiving system produces is a translation of what the sending system provided.
* A warrant is computed within one context. A proof in the format's native logic can be
  re-checked in any context. A proof in another logic carries only as far as the translation
  described in the previous item. A record of grounds does not carry at all: a reader in
  another context must examine its premises afresh. Phase 1 does not establish that a warrant
  resting on grounds survives transport to another context.

### Open questions

The group states the following questions because they determine where the Phase 1 guarantees
stop, and because they define the substance of any Phase 2 work. Both questions share a
structure: a protocol that moves data, specified against a theory that operates on statements.

1. **Statements and combination.** When combining records changes the vocabulary an existing
   statement refers to, what must the combination establish? Re-checking every statement is
   sound but does not scale, and it answers a different question: it reports whether something
   broke, rather than what combination means for a body of recorded claims. A tractable
   formulation treats a recorded verdict as a cached result whose read set is its footprint,
   and seeks the condition under which that result is invalidated.
2. **Statements and translation between reasoning systems.** A translation should carry
   statements, not only values. An answer requires three components: a statement translation
   that can be checked when it is declared; transport of the justification together with the
   statement it justifies; and a treatment of approximate translation that constrains what a
   recipient may conclude, rather than only recording that the translation was approximate.

   Where truth in both systems means having a proof, as in a type theory, the first component
   reduces to a narrower condition: the translated statement has a proof exactly when the
   original does. A checker for such a system accepts a proof only of a statement that holds,
   so the soundness obligation falls entirely on the translation. This condition is easier to
   exhibit than preservation of models, but it still cannot be checked by machine, because the
   two proofs belong to different theories and the translation maps statements rather than
   proofs.

The group does not address either question in Phase 1.

### Existing implementation experience

An open-source implementation exists that covers most of the Phase 1 concepts and both
Phase 1.5 concepts. It provides evidence that the concepts compose, and it is a source of the
specific problems the specifications address. The group does not standardize that
implementation. Specifications produced under this Charter MUST be implementable by parties
who have no relationship to it.

Two concepts listed above are not implemented in it. Statement translation between reasoning
systems is not implemented and is not an implementation task, because no formulation exists to
implement. Validation of a combined record is implemented only in part.

The implementation's representation of justification changed after the first draft of this
Charter. It previously assigned one of four warrant categories to each resource, based on the
resource's class or the process that wrote it. It now computes warrant from three grounds and
holds proofs and records of grounds as separate kinds of object. The group treats this change
as evidence that the Phase 1 vocabulary is not yet settled, and as a source of specific
failures the specification must exclude.

### Out of Scope

The following are deferred to a later phase. They remain within the group's interest and
outside this Charter's deliverables.

* Combining independently produced records, and any conformance requirement that presumes
  more than one producer. Deferred to Phase 1.5.
* Preservation of existing statements when records are combined; statement translation
  between reasoning systems; transport of justification across such a translation; and the
  treatment of approximate translation. Deferred to Phase 2.
* Any conformance requirement that would oblige an implementation to establish one of the
  above.

The following are outside the group's scope entirely. The group will not work on them under
this Charter or a successor.

* Reasoning engines, proof assistants, and type checkers as such. The group defines the
  records these systems produce and consume, not the systems.
* Methods for ranking or scoring the trustworthiness of sources. The format records how a
  fact is known. It does not evaluate whether a given source is reliable. A declared ground
  names the party a claim relies on; whether to rely on that party is the reader's decision.
* Identity and credential systems. Where a record must name an actor, the format references
  existing work rather than defining a replacement.
* Domain vocabularies. The group defines how vocabularies are imported and referenced, not
  their content.
* Storage systems, wire protocols, and query language syntax, except where these are
  observable in the format itself.
* Policy. What a publisher, funding body, or regulator requires is outside the group's scope,
  although the group intends its deliverables to make such requirements expressible in
  checkable terms.

---

## Deliverables

### Specifications

**S1 — AI Computed Provenance Format.** A Community Group Report, normative in intent,
covering the Phase 1 concepts: the graph and vocabulary layer, canonical encoding and content
addressing, the statement language with its judgement form and typing rules, provenance and
its mapping to PROV-O, justification terms and the computation of warrant, and the declaration
surfaces for reasoning systems and translations between them.
S1 does not cover the combination of independently produced records.

Each part of S1 MUST carry its own conformance requirements, so that a partial implementation
can state which parts it satisfies. Each part whose guarantees stop at a context boundary MUST
carry the corresponding conditional statement described above in normative text.

The group aims to publish a first public draft of S1 by {TBD: YYYY-MM-DD} and a stable
version by {TBD: YYYY-MM-DD}, pending consensus.

**S2 — Combining Independently Produced Records.** A Community Group Report covering the
Phase 1.5 concepts: the taxonomy of conflicts, the available resolutions, and the requirement
that a combined record satisfies the validation its inputs satisfied.

S2 is a separate document rather than a revision of S1, so that an implementation conforming
to S1 does not become non-conforming when S2 is published. An implementation that conforms to
S1 and not to S2 constitutes a recognized conformance profile and MUST NOT be characterized as
deficient on that basis alone.

S2 falls outside this Charter's scope and requires an amendment before the group may publish
it. See [Work Limited to Charter Scope](#work-limited-to-charter-scope).

Editors for each specification are to be determined by the group.

### Non-Normative Reports

The group may produce other Community Group Reports within the scope of this charter that are
not Specifications, such as use cases, requirements, or white papers.

The group intends to produce two.

**R1 — Architectural Guidelines: Inspectability and Independent Verification.** A description
of what a deployment exposes in order for a third party to check its output, and which design
choices prevent independent verification. The report identifies specific patterns to avoid,
including verdicts whose inputs cannot be re-derived, canonical forms that depend on an
unpublished implementation, vocabulary extension points available to only one party, warrants
stored as fields that can disagree with the evidence, claims recorded more broadly than what
was established, and reasoning systems that collapse an analysis plan and its data into a
single opaque ground.

**R2 — Primer.** An introduction to the format for practitioners in scientific and technical
fields who are not specialists in formal methods.

### Test Suites and Other Software

The group **may** produce test suites to support the specifications. See the GitHub `LICENSE`
file for test suite contribution licensing information.

The group intends to produce a conformance test suite for S1, comprising:

* test vectors pairing input content with its expected canonical form and identifier;
* validation cases, both conforming and non-conforming; and
* end-to-end cases pairing a record with the result a conforming checker reaches.

The suite MUST include cases that a conforming implementation fails if it presumes a guarantee
the specification defers to a later phase. It MUST also include cases that a conforming
implementation fails if it lets a claim become verified without a checked judgement: a record
of grounds offered as a proof, a warrant asserted by the producer, a verdict offered without a
proof object, and a justification whose support is circular. A test suite that cannot detect
an implementation claiming more than the specification establishes does not test the
boundaries this Charter defines.

The test suite MUST be executable against an implementation that the group does not maintain.

---

## Neutrality and Independent Implementation

The group's mission requires that the verification layer remain neutral. The following
commitments give that requirement effect. They apply in addition to the W3C Community and
Business Group Process.

* Specifications and test suites produced by the group MUST be published under terms that
  permit any party to implement them.
* A feature SHOULD advance to a stable specification only after a second, independent
  implementation exists. Where the group advances a feature without one, the specification
  MUST record that fact and the reason.
* A specification produced by the group MUST NOT depend normatively on a document that the
  group cannot cite publicly.
* Chairs SHOULD NOT all be affiliated with the same organization.

Participants note that the party proposing this group also maintains the existing reference
implementation described above. The second-implementation expectation and the expectation
regarding Chair affiliation are the group's response to that concentration.

---

## Dependencies or Liaisons

The group depends on, or coordinates with, the following work.

* **W3C Provenance (PROV-DM, PROV-O).** The most closely related work. The format's
  provenance records map onto PROV-O, and the specification defines that mapping. PROV treats
  an entity as opaque and does not model what it states. This group specifies warrant, which
  concerns exactly that content and which PROV cannot represent. The two are complementary.
  The group intends to propose a liaison.
* **RDF, RDF-star, JSON-LD, and SHACL.** Adopted by reference for serialization and shape
  constraints.
* **Verifiable Credentials and Decentralized Identifiers.** Adopted by reference where a
  record names an actor or carries a signed assertion.
* **C2PA.** Content provenance for media. A different class of asset, with a comparable
  approach to cryptographic chaining.
* **in-toto, SLSA, and SPDX.** Supply chain attestation. The closest existing analogue to
  establishing that a computation occurred, and relevant prior art for the conformance test
  suite.
* **RO-Crate, nanopublications, and CodeMeta.** Research object packaging, and a likely
  integration surface.
* **RFC 8785, RFC 8949, and the COSE and JOSE families.** Canonicalization and signing
  primitives.
* **OBO Foundry, the UMLS Metathesaurus, and WordNet.** Vocabulary dependencies. The UMLS
  Metathesaurus carries licensing obligations that the format represents, as described in
  Scope of Work.

---

## Community and Business Group Process

The group operates under the [Community and Business Group
Process](https://www.w3.org/community/about/process). Terms in this
Charter that conflict with those of the Community and Business Group
Process are void.

As with other Community Groups, W3C seeks organizational licensing
commitments under the [W3C Community Contributor License Agreement
(CLA)](https://www.w3.org/community/about/process/cla/). When people
request to participate without representing their organization's legal
interests, W3C will in general approve those requests, with the
following understanding: W3C will seek and expect an organizational
commitment under the CLA starting with the individual's first request
to make a contribution to a group [Deliverable](#deliverables). The
section on [Contribution Mechanics](#contribution-mechanics) describes how W3C
expects to monitor these contribution requests.

The [W3C Code of Conduct](https://www.w3.org/policies/code-of-conduct/) and [W3C Antitrust and competition policy](https://www.w3.org/policies/antitrust-2024/) apply to participation in this group.

---

## Work Limited to Charter Scope

The group will not publish specifications on topics other than those listed under
[Specifications](#specifications). See below for
[how to modify the charter](#amendments-to-this-charter).

---

## Contribution Mechanics

Substantive contributions to specifications can only be made by Community Group Participants who have agreed to the [W3C Community Contributor License Agreement (CLA)](https://www.w3.org/community/about/process/cla/).

Reports other than Specifications published by this group should use the [W3C Software and Document License](https://www.w3.org/copyright/software-license-2023/) where possible.

Community Group participants agree to make all contributions in the GitHub repository the group is using for the particular document — e.g., via pull requests, issues, or comments on existing issues.

All GitHub repositories attached to the Community Group must contain a copy of the [CONTRIBUTING](https://github.com/w3c/licenses/blob/main/CG-CONTRIBUTING.md) and [LICENSE](https://github.com/w3c/licenses/blob/main/CG-LICENSE.md) files.

---

## Transparency

The group will conduct all technical work in public. If the group uses GitHub, technical work will occur in its GitHub repositories (and not privately on mailing lists).

Meetings may be restricted to Community Group participants, but a public summary or minutes must be posted to the group's public mailing list or as an issue on GitHub.

---

## Decision Process

This group will seek to make decisions where there is
consensus. Groups are free to decide how to make decisions
(e.g., Participants who have earned Committer status for a
history of useful contributions assess consensus, or the Chair
assesses consensus, or where consensus isn't clear there is a
Call for Consensus [CfC] to allow multi-day online feedback for
a proposed course of action). It is expected that participants
can earn Committer status through a history of valuable
contributions, as is common in open source projects. After
discussion and due to consideration of different opinions, a
decision should be publicly recorded (where GitHub is used as
the resolution of an Issue).

If substantial disagreement remains (e.g., the group is divided) and
the group needs to decide an Issue in order to continue to make
progress, the Committers will choose an alternative that had
substantial support (with a vote of Committers if
necessary). Individuals who disagree with the choice are
strongly encouraged to take ownership of their objection by
taking ownership of an alternative fork. This is explicitly
allowed (and preferred to blocking progress)
to let implementation experience inform which spec is
ultimately chosen by the group to move ahead with.

Any decisions reached at any meeting are tentative and should be
recorded in a GitHub Issue for groups that use GitHub and
otherwise on the group's public mail list. Any group participant
may object to a decision reached at an online or in-person
meeting within 7 days of publication of the decision provided
that they include clear technical reasons for their
objection. The Chairs will facilitate discussion to try to
resolve the objection according to this decision process.

It is the Chairs' responsibility to ensure that the decision process
is fair, respects the consensus of the CG, and does not
unreasonably favor or discriminate against any group
participant or their employer.

---

## Chair Selection

Participants in this group choose their Chair(s) and can replace their
Chair(s) at any time using whatever means they prefer. However, if 5
participants, no two from the same organization, call for an election,
the group must use the following process to replace any current Chair(s)
with a new Chair, consulting the Community Development Lead on election
operations (e.g., voting infrastructure and using [RFC 3797](https://datatracker.ietf.org/doc/html/rfc3797)).

Participants choose their Chair(s) and can replace them at any time. However, if 5 participants (no two from the same organization) call for an election, the group must use the following process, consulting the Community Development Lead on election operations:

* Participants announce their candidacies. Participants have 14 days to
announce their candidacies, but this period ends as soon as all
participants have announced their intentions. If there is only one
candidate, that person becomes the Chair. If there are two or more
candidates, there is a vote. Otherwise, nothing changes.
* Participants vote. Participants have 21 days to vote for a single
candidate, but this period ends as soon as all participants have
voted. The individual who receives the most votes, no two from
the same organization, is elected chair. In case of a tie,
RFC 3797 is used to break the tie. An elected Chair may appoint
co-Chairs.

Participants dissatisfied with the outcome of an election may ask the
Community Development Lead to intervene. The Community
Development Lead, after evaluating the election, may take any
action including no action.

---

## Amendments to This Charter

The group can decide to work on a proposed amended charter, editing
the text using the [Decision Process](#decision-process) described above. The
decision on whether to adopt the amended charter is made by conducting
a 30-day vote on the proposed new charter. The new charter, if
approved, takes effect on either the proposed date in the charter
itself, or 7 days after the result of the election is announced,
whichever is later. A new charter must receive 2/3 of the votes cast
in the approval vote to pass. The group may make simple corrections to
the charter such as deliverable dates by the simpler group decision
process rather than this charter amendment process. The group will use
the amendment process for any substantive changes to the goals, scope,
deliverables, decision process or rules for amending the charter.

Opening Phase 1.5 or Phase 2, as described under Scope of Work, constitutes a substantive
change to the scope and deliverables of this Charter, and therefore requires an amendment
under this section. The group SHOULD consider such an amendment when the following conditions
are met.

* For Phase 1.5: two or more implementations, or two or more deployments, hold records that
  require combination, and S1 is stable enough to extend.
* For Phase 2: at least one of the open questions stated under Scope of Work has a written
  formulation that the group judges precise enough to specify against. A formulation is
  sufficient; an implementation is not required.

Phase 1.5 is not a prerequisite for Phase 2.

## **Appendix: Glossary of Mathematical Terms**

This glossary provides high-level definitions of the mathematical, logical, and categorical concepts referenced in the scope of this Charter. It is intended to assist stakeholders in understanding the formal mechanisms underpinning the specification.

* **Comorphism:** A translation mechanism between two different logical systems. It maps the rules and statements of one system into another, allowing them to exchange conclusions while preserving their strict mathematical meaning.  
* **Factive:** A property of evidence. Evidence is factive if its existence entails that the proposition it concerns holds. A checked proof is factive; a record of grounds is not.  
* **Grothendieck Construction:** A mechanism for unifying multiple distinct reasoning systems into a single interface. It enables a participant or automated system to dispatch queries across various logical frameworks simultaneously.  
* **Ground:** One of three kinds of support at the leaves of a justification term: verified (a checked proof of the proposition itself), observed (a recording occurred), and declared (a named party asserted the proposition).  
* **Judgement:** A checked statement that a term inhabits a type in a named logic. When the type is a proposition, a judgement records that a checker verified a proof of it.  
* **Justification Term:** A structured object recording the grounds on which a claim rests and how they combine. It is not factive: it records what a claim rests on without asserting the claim.  
* **Provenance:** The record of how a resource came to exist. Every resource has provenance. It is independent of warrant and maps onto W3C PROV.  
* **Pushout of a Span:** A categorical structure used to mathematically model the combination of records. When two parties independently extend a shared record, they create two divergent branches from a common ancestor; the pushout defines the blueprint for a logically consistent merge that preserves new information and resolves structural conflicts without violating underlying constraints.  
* **Theory of Institutions:** A formal framework used to define what constitutes a "logic". Because different scientific domains use fundamentally different logics to establish facts (e.g., statistical tests versus mathematical proofs), this theory provides a neutral foundation for safely relating and comparing these varying systems.  
* **Warrant:** The standing of a claim, computed from its justification term: which parts are proved, which rest on observations, and which rest on declarations by named parties. A warrant is never stored or asserted.