# Contributing to The Missing Layer

Thank you for your interest. This is a request for comment, and engagement is welcome.

---

## What is open for comment

**The Protocol Specification (rfc.md)** is a living draft. The following areas are actively seeking feedback:

- **Invariant completeness.** Are eight invariants sufficient? If you believe a ninth is required, you must demonstrate that it cannot be expressed as a consequence of the existing eight.
- **Enforcement boundary clarity.** Are the three mandatory enforcement points (pre-action resolution, Policy evaluation, Provenance emission) specified with sufficient precision for implementation?
- **Error semantics.** Are the four failure modes complete? If you identify a fifth, describe the failure, the expected behavior, and why it is not covered by existing error handling.
- **Conformance testability.** Can the two conformance levels (Core and Verifiable) be tested by an independent party? If not, identify what is ambiguous.
- **Security considerations.** Are the five threat vectors complete? If you identify a sixth, map it to existing mitigations or demonstrate why new mitigation is required.
- **Temporal properties.** Is the MAY declare / MUST honor boundary in the right place?

---

## What is not open for comment

**The Architectural Declaration (manifesto.md)** is frozen. It defines the architectural philosophy and is not subject to revision.

**The nine primitives** are a closed set. Proposals to add a tenth primitive or remove an existing one will not be accepted. If you believe a concept is missing, demonstrate how it can be expressed as an instance of, property of, or relationship between the existing nine. If it cannot, it is out of scope.

**The architecture/implementation boundary.** This specification does not dictate implementation mechanism. Proposals that require a specific language, runtime, transport protocol, or storage mechanism are out of scope.

---

## How to contribute

**Open an issue.** Describe what you're responding to, cite the specific section, and make your case. Precision is valued over volume.

**If you have an implementation.** If you've built something that conforms (or attempts to conform) to this specification, open an issue describing your experience. What worked. What was ambiguous. What you had to decide that the spec didn't address. Implementation experience is the most valuable feedback a specification can receive.

**If you disagree.** Good. State your position, cite your reasoning, and engage in good faith. The architecture was designed to be argued against without collapsing. Test it.

---

## What a good issue looks like

- References a specific section and requirement (e.g., "Section 7.2 — Mandatory Policy Evaluation")
- States the problem or question clearly
- Proposes a resolution or asks a precise question
- Does not require reading the contributor's entire project to understand

---

## Code of conduct

Engage with rigor and respect. This is architecture for coherence — the discussion should reflect that.
