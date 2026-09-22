# Tiinex: Practical Observations from an Artifact-Based Continuity Experiment

**Status:** Practitioner note / ongoing experiment  
**Version:** 0.1  
**Date:** 2026-09-15  
**Author:** Olle Tiinus  

---

## Status and intent

This document describes observations from an ongoing implementation experiment.

It is not a proposal for a standard, not a replacement for W3C PROV, and not a claim that the distinctions described here are novel.

The purpose is narrower:

- to show a concrete continuity workflow that has been exercised repeatedly,
- to describe failure modes observed during those runs,
- to explain which design boundaries emerged in response,
- and to make it easier to compare those observations with existing provenance work and with the evolving scope of the AI Computed Provenance Community Group.

Tiinex is still under active refactoring.

Some tooling is incomplete, some workflows still require manual grounding or retrospective inspection, and the current implementation should be read as experimental evidence rather than a finished architecture.

---

## 1. Original problem

The initial problem was practical:

> How can ongoing work continue across separate LLM or tool environments without depending on proprietary conversation memory, hidden application state, or one specific model provider?

The desired property was not simply archival.

A preserved conversation or source file may explain what happened previously, but that does not necessarily provide enough information for another person, model, or tool to safely continue the work.

The experiment therefore gradually shifted from preserving conversation history toward preserving a portable representation of work state.

That representation needs to answer questions such as:

- What is this work about?
- Where did the current state come from?
- What has already happened?
- Which constraints still apply?
- Which responsibilities have moved?
- Which responsibilities have not moved?
- What context is required to continue?
- What is merely reference material?
- What remains unresolved?
- What may the next participant do?

The current working term for this is **work continuity**.

---

## 2. Current experimental model

The system is artifact-based.

Human-readable files are used to preserve declared work state, lineage, context, Handoff information, and validation metadata.

The current workflow typically involves several isolated contexts with different roles.

A simplified example is:

    Anchor
      |
      | Handoff
      v
    Specialist
      |
      | work / observation / result
      v
    Return Handoff
      |
      v
    Anchor
      |
      | reconciliation
      v
    continued work

The names of individual roles are implementation-specific.

The more important property is that the contexts are intentionally separated.

A receiving context should not rely on hidden conversation state from the sender.

It instead receives an explicit artifact package describing what it needs in order to continue its assigned part of the work.

---

## 3. Human-mediated transport

The current transport between contexts is usually performed by a human.

For example:

1. one context produces a Handoff package,
2. the human makes that package available to another context,
3. the receiving context cold-starts from the package,
4. the receiving context performs its work,
5. a return package is produced,
6. the human routes that package back for reconciliation.

This is partly an implementation constraint and partly an economic one.

The current experiment often uses flat-rate interactive model environments rather than metered API orchestration.

This makes substantial model work practical at fixed cost while transport remains manually mediated.

The human therefore acts primarily as a thin routing and control layer rather than as the computational worker.

Manual transport is not intended as a semantic requirement of the artifact model.

A future automated runtime could perform equivalent transport, provided that it preserves the same explicit boundaries and does not silently infer permission, invent missing context, or change the declared meaning of transferred work.

---

## 4. Parallel work

The workflow is not necessarily linear.

An Anchor context may delegate several bounded tasks independently:

                        -> Specialist A ->
                       /                  \
    Anchor -> Handoffs -> Specialist B ----> returns -> reconciliation
                       \                  /
                        -> Specialist C ->

In the current implementation, the human routes these transfers manually.

This has remained practical because the routing step is small relative to the time spent by each model context performing its task.

A specialist may perform several minutes of work while the corresponding human routing action takes only a small amount of attention.

This allows multiple independent work branches to remain active without requiring a central automated agent runtime.

---

## 5. Continuation as the practical success condition

A Handoff is useful only if the receiving context can reconstruct enough of the work state to continue appropriately.

The experiment has therefore treated successful continuation as something stronger than transport alone.

An informal description of the current target is:

    available context
    +
    declared meaning
    +
    lineage
    +
    responsibility / permission boundaries
    +
    sufficient model grounding
    =
    usable continuation

This is not a formal equation.

It is simply a summary of several conditions that repeated implementation runs have exposed.

---

## 6. Observed failure mode: available context is not the same as grounded context

One recurring implementation-level observation has been:

> Context can exist in the transported package without the receiving model successfully incorporating it into its effective working understanding.

In other words:

    context available
    !=
    context grounded

A package may contain the expected files, lineage, role definitions, and instructions while the receiving model still fails to discover or interpret part of them during its initial run.

The current workflow sometimes detects this through retrospective inspection.

A receiving model may be asked to perform a fuller discovery pass after its initial grounding, exposing information that existed in the package but was not incorporated into the first interpretation.

At present I treat this primarily as a Tiinex/application-level failure mode.

I do not yet know whether "grounding completeness" has a useful representation at a provenance or justification layer, or whether it should remain entirely outside that scope.

---

## 7. Fail-closed continuation checks

Some current Tiinex workflows deliberately fail closed.

For example, if a requested continuation route points to a lineage location that is not accepted as a current continuation frontier under Tiinex's own rules, the receiving context should not silently select a nearby or plausible alternative.

It should stop and report the mismatch.

The intended application-level invariant is approximately:

> Missing or ambiguous permission to continue should not be repaired by model inference.

This does not mean that Tiinex introduces validation where provenance systems have none.

W3C PROV has its own formal constraints and validity conditions for determining whether a provenance description represents a consistent possible history.

The Tiinex check described here asks a different, application-specific question:

> Given this represented work state, is this particular requested continuation acceptable under the declared workflow rules?

A model may be capable of inventing a plausible continuation.

Plausibility is not the same as permission to continue from a particular state.

---

## 8. Provenance, integrity, meaning, and prospective operational permission

One practical outcome of the experiment has been a need to keep several concerns separate.

### Provenance

Where did this artifact, state, or decision come from?

### Integrity

Does the artifact correspond to the representation whose content was previously identified or validated?

### Declared meaning

What does the artifact state that it represents?

### Prospective operational permission

Who or what is permitted, under the current workflow, to continue, modify, reconcile, publish, or otherwise act from a particular represented state?

These concerns are related but are not treated as interchangeable in the current implementation.

For example:

    known provenance
    does not imply
    semantic correctness

    integrity verification
    does not imply
    truth

    evidence
    does not by itself imply
    permission to perform a future action

    lineage
    does not by itself imply
    permission to continue from a particular node

This requires an important qualification when comparing Tiinex with PROV.

PROV already supports responsibility, association and delegation, including relationships in which one agent acts on behalf of another.

The question raised by Tiinex is therefore not simply whether provenance can represent "authority".

A more precise question is:

> How should retrospective records of responsibility or delegation be distinguished from prospective permission to continue or mutate work from a particular state?

I do not yet know whether an established model already covers this distinction adequately.

---

## 9. Roles and responsibility transfer

A Handoff may identify a sender and receiver, but practical runs have shown that those two participants are not always sufficient to represent all relevant context.

A third participant may remain semantically relevant even when it is not the transport sender or receiver.

For example:

    From: Anchor
    To: Specialist

    Relevant participant:
    Sigma

If the relevant participant is defined in a workspace that is intentionally excluded from the Handoff, the receiving context may retain a syntactically valid reference while lacking enough information to interpret that reference correctly.

This has exposed a current design problem:

> How much information must accompany a reference in order for that reference to remain meaningfully grounded after transport?

One design direction currently being explored is a bounded cached representation:

    excluded source workspace
            |
            v
    cached minimal representation
            |
            v
    pointer carried by transported artifact

The goal is not to import the entire excluded workspace.

The goal is to preserve enough of the referenced participant or role definition for the receiving context to interpret the pointer appropriately.

This part of the implementation is not complete.

There appears to be a potentially useful structural comparison here with the Community Group's developing work on statements and justification crossing context boundaries.

I do not assume that these are the same problem.

The Tiinex case may prove to be only an application-level grounding concern.

I would be interested in understanding whether there is a deeper relationship.

---

## 10. Handoffs are not only lineage edges

A Handoff currently tries to preserve more than ancestry.

Depending on the workflow, it may distinguish between:

- transferred responsibility,
- retained responsibility,
- required context,
- reference context,
- explicit exclusions,
- dependencies,
- completion expectations,
- transport information.

The practical motivation is that two artifacts may have a clear provenance relationship while still being insufficient for continuation.

A receiving participant may need to know not only where the work came from, but also:

> What am I now expected or permitted to do?

and:

> What remains outside this Handoff?

That distinction has proven useful in multi-context work.

---

## 11. Why the current experiment remains inspectable

The implementation currently keeps most declared state in human-readable artifacts.

This has several practical benefits during experimentation:

- a human can inspect what was actually transferred,
- a model can be asked to read the same representation,
- failures can be reconstructed after the fact,
- tool behavior can be compared against declared artifact meaning,
- changes can be versioned using ordinary file-based workflows,
- continuation does not depend entirely on one application's hidden state.

The current implementation also uses validation and integrity mechanisms.

These are still being refactored and should not be interpreted as a complete formal verification system.

---

## 12. Relationship to W3C PROV and adjacent work

Tiinex has been influenced by provenance concepts, including work in the W3C ecosystem.

The current effort is not intended to replace PROV.

PROV already provides a substantial model for describing entities, activities, agents, derivation, responsibility, delegation, roles, plans, revisions, and related provenance structures.

It also defines constraints under which a provenance instance is considered valid.

The useful questions for Tiinex are therefore not whether these concepts exist, but rather:

- Which Tiinex concepts are direct applications or specializations of existing provenance concepts?
- Which are implementation-specific names for already established ideas?
- Which belong to application-level continuity rather than provenance?
- Which practical failure modes overlap with the Community Group's work on statements, justification, mechanically checkable reasoning, and context boundaries?

The area I am still trying to understand is how Tiinex's continuation-oriented concerns relate to the group's more formal treatment of meaning and justification.

Questions that repeatedly arise in the experiment include:

- Is the available context sufficient for continuation?
- What meaning should be inherited?
- What remains unresolved?
- Which responsibility moved?
- Which responsibility stayed behind?
- Who may perform the next operation under the workflow?
- Which references remain interpretable after part of their source context is excluded?
- Can a receiving system mechanically determine that a requested continuation should stop?

Some of these may already have established treatments elsewhere.

Identifying that is one purpose of sharing the experiment.

---

## 13. A typical practical continuity run

A current run may look approximately like this:

1. Anchor identifies a bounded task.

2. Anchor produces a Handoff artifact.

3. The Handoff declares relevant information such as:
   - target participant,
   - current lineage position,
   - required context,
   - relevant references,
   - responsibility being transferred,
   - completion expectations.

4. A human routes the package to an isolated specialist context.

5. The specialist cold-starts from the package.

6. The specialist or supporting tooling inspects the declared route and available context.

7. If grounding or continuation state is inconsistent:
   - stop,
   - report the inconsistency,
   - do not silently choose a different continuation.

8. If the context is sufficient:
   - perform bounded work,
   - preserve observations or results,
   - produce a return Handoff.

9. The human routes the return to Anchor.

10. Anchor inspects and reconciles the result.

11. The resulting state becomes a basis for further work or delegation.

This process remains imperfect.

Some runs still require additional grounding instructions or retrospective discovery.

Those failures are currently treated as observations that inform the next iteration of the artifact and tooling design.

---

## 14. What has improved during the current refactor

The current refactor has not yet reached feature parity with an earlier Site proof of concept.

However, several workflow properties have improved:

- transport structure is becoming more consistent,
- tooling increasingly assists rather than obstructs the workflow,
- isolated model contexts are grounding assigned roles more reliably,
- repeated Handoffs require less ad hoc explanation than earlier versions,
- fail-closed continuation behavior is becoming more explicit.

Important missing or incomplete areas currently include:

- fuller participant-role pointer handling,
- cached supporting context for references whose source workspace is excluded,
- more complete validator porting,
- schema-building workflow,
- more reliable automatic discovery and grounding,
- rebuilt Site functionality,
- current Playthings work,
- reduced need for retrospective grounding checks.

These gaps are part of the present experimental state.

---

## 15. Earlier Site proof of concept

An earlier Site proof of concept explored human-facing navigation of the same general problem space.

It included views such as:

- feed,
- tree,
- lineage,
- workspaces,
- filtering,
- history / time-oriented navigation,
- import / export,
- GitHub-oriented workflows.

That implementation was useful for exploring how humans navigate continuity and lineage.

The current refactor is rebuilding around stricter artifact semantics and has not yet restored all of that functionality.

The older Site should therefore be understood as a proof of interaction concepts rather than the current implementation.

---

## 16. Current interpretation

The strongest current observation is not that Tiinex has discovered a new provenance model.

It is that repeated cross-context continuation makes several boundaries operationally visible:

    recorded history
    is not automatically
    sufficient continuation state

    context available
    is not automatically
    context grounded by a receiving model

    provenance or delegation
    does not automatically determine
    future workflow permission

    integrity
    is not automatically
    semantic truth

    successful transport
    is not automatically
    successful semantic transfer

The implementation is currently trying to make these boundaries explicit enough to inspect and, where appropriate, automate safely.

---

## 17. Questions for comparison and discussion

The following questions are currently more useful to me than asserting answers:

1. Which of these distinctions already map directly to PROV or related W3C work?

2. How should retrospective responsibility or delegation be related to prospective permission to continue or mutate work from a particular represented state?

3. Are there established models that address what information must survive a context boundary for a recipient to continue correctly?

4. How should a portable artifact preserve the meaning of references whose original context is intentionally excluded?

5. Is the observed distinction between available context and effective model grounding useful outside the application layer, or should it remain an implementation concern?

6. How does the Community Group's treatment of statement and justification transport relate, if at all, to practical continuity across isolated model contexts?

7. Where should the boundary lie between:
   - recorded provenance,
   - declared semantics,
   - mechanically inspectable reasoning,
   - and application-specific permission or workflow policy?

8. Which parts of the Tiinex experiment are simply implementation choices that should remain outside any shared provenance or reasoning specification?

---

## 18. Scope of my contribution

The value I can currently offer is primarily empirical.

I have been repeatedly exercising this workflow across isolated LLM contexts and observing where continuation succeeds, where it fails, and which distinctions reduce repeated friction.

My background is implementation and systems architecture rather than formal methods.

I am still learning the relevant W3C provenance and formal work in more detail.

For that reason I would prefer these observations to be read as implementation evidence and questions rather than claims about how shared provenance or reasoning specifications should be designed.

If parts of the experiment reproduce already-solved problems, identifying that is useful.

If some failure modes fall outside the group's intended scope, identifying that is also useful.

The goal is to understand the boundary more accurately.

---

## 19. Longer-term motivation

The immediate motivation is practical.

I want ongoing work to survive movement between people, tools, and model environments without making one provider's hidden state the only place where continuity exists.

If the experiment works, it should make my own projects easier to continue over long periods.

One deliberately demanding future use case is an artifact-native role-playing runtime where players, game-master processes, tools, and model contexts can exchange bounded state while preserving explicit control over what becomes fictional truth.

That use case is still experimental.

It is useful mainly because it stresses many of the same continuity problems:

- multiple concurrent participants,
- partial knowledge,
- explicit responsibility and permission boundaries,
- persistent world state,
- conflicting inputs,
- Handoffs,
- lineage,
- reconstruction after context loss.

---

## 20. Summary

Tiinex is currently best understood as an ongoing artifact-based continuity experiment.

Its present contribution is not a proposed replacement for provenance standards or formal reasoning work.

It is a set of practical observations from repeatedly moving work between isolated contexts and asking:

> What must remain explicit for another participant to continue correctly?

The current evidence suggests that provenance is an important part of that problem, but not the entire application-level problem.

The purpose of sharing the work is to understand those boundaries more accurately.
