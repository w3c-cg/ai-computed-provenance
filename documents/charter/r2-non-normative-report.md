# **R2 — Primer: AI Computed Provenance Format**

*This document serves as an initial draft outline for the R2 Non-Normative Report.*

## **1\. Introduction**

This Primer introduces the AI Computed Provenance Format for practitioners in scientific and technical fields. While the underlying specifications rely on formal methods and category theory, this document explains the format's mechanics in practical terms. Its goal is to demonstrate how to record, and independently check, how a fact came to be known in an AI-assisted scientific or technical workflow.

## **2\. The Core Distinction: Computed vs. Asserted**

The format operates on a foundational distinction between asserting a result and proving how a result was computed.

* **Asserted Records:** A system simply states a conclusion. Checking an asserted result requires trusting the producer, as the statement offers no internal proof of its derivation.  
* **Computed Records:** A system provides a checkable proof of its derivation. A second party holding the record and the specification can re-derive the recorded conclusions without access to the producing system.

## **3\. Warrant Categories and Justification**

The format assigns a specific warrant category to every recorded fact. This category is strictly computed from how the record was generated; a conforming implementation MUST NOT permit a producer to self-declare a claim of verification.  
The warrant categories are:

* **Declared:** Information explicitly stated as a premise or assumption.  
* **Observed:** Data captured directly from an external source or instrument.  
* **Derived:** Conclusions reached through logical or statistical operations within a single system.  
* **Verified:** Conclusions independently checked against the recorded justification.

## **4\. The Knowledge Graph and Immutable Layers**

Records in this format accumulate as immutable layers within a knowledge graph.

* **Content Addressing:** Each layer is identified by a cryptographic hash of its exact content. Because content is never modified in place, citing a fact guarantees a citation to a specific, immutable state. Two parties MUST compute the same identifier for the same content.  
* **Self-Contained Vocabulary:** Vocabulary declarations reside within the same record as the instances that use them. This ensures that changes to external configurations do not silently alter the meaning of a recorded fact.

## **5\. Bridging Different Systems (Federated Reasoning)**

Scientific workflows often combine statistical results, numerical bounds, and machine-checked proofs. The format uses a mathematical framework (the theory of institutions) to translate securely between these distinct reasoning systems. This ensures that when a statistical tool passes a bound to a numerical solver, the logical integrity of the conclusion remains intact and checkable.

## **6\. Merging Divergent Work (Combining Records)**

When multiple researchers or automated agents independently extend a shared dataset, their work must eventually be combined. The format utilizes a structured mathematical merge (a pushout of a span) to combine divergent records logically. This guarantees that all combined records satisfy the exact same structural validation rules as their original inputs, explicitly flagging conflicts before they corrupt the unified graph.