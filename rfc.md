# The Missing Layer: Protocol Specification

*Request for Comment — Draft 1*

---

## Abstract

This document specifies the protocol requirements for implementing The Missing Layer, an architectural substrate for coherence wherever AI-mediated systems operate faster than humans can synchronize.

The Missing Layer defines nine irreducible primitives, a set of invariants, mandatory enforcement boundaries, error semantics, and conformance levels. Together these establish the minimum structural requirements for any system in which intent must remain intelligible as complexity increases.

This specification is normative. It is intended for implementors, auditors, and standards reviewers. For the architectural philosophy, design rationale, and motivating problem statement, see the companion document: *The Missing Layer: Architectural Declaration* (the Manifesto).

---

## Status of This Document

This is a draft specification circulated for public comment. No portion of this document is final. Feedback is invited on all sections.

---

## 1. Terminology and Conventions

### 1.1 Keywords

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in RFC 2119.

### 1.2 Definitions

**Caller.** A runtime identity — human or machine — operating within a Scope and assigned an Archetype.

**Capability.** The architectural primitive identifying a unit of value by the outcome it delivers. A Capability is not a capability-based security token (an unforgeable reference granting access), nor a strategic planning construct (as in enterprise capability mapping), nor a runtime service. A Capability exists independently of whatever implements it.

**Declaration.** A versioned, validated, diffable representation of all primitive instances and their relationships within a system. The Declaration is the single authority for the system's intended architectural state.

**Primitive.** One of nine irreducible architectural shapes defined by this specification. Nothing outside these nine is a primitive.

**Significant action.** Any Capability invocation, Connector read, Binding write, Policy evaluation resulting in deny, or modification to the Declaration itself.

**Conformant implementation.** A system that satisfies the requirements of at least one conformance level defined in this specification.

### 1.3 Architecture, Infrastructure, Implementation

This specification distinguishes three layers:

**Architecture** is what the system is — the primitives, relationships, and invariants defined in this document.

**Infrastructure** is what the architecture provides — the substrate on which products and systems are built. The Missing Layer is architecture that functions as infrastructure.

**Implementation** is how the architecture is realized — the skills, runtimes, platforms, and mechanisms that fill the primitive shapes. This specification constrains what properties implementations MUST satisfy. It does not constrain how.

---

## 2. Scope of This Specification

This specification defines:

- The nine architectural primitives and their relationships
- The Declaration and its integrity requirements
- Eight invariants that MUST hold in all conformant implementations
- Three mandatory enforcement boundaries
- Error semantics for four failure modes
- Temporal property constraints
- Two conformance levels
- A threat model with mitigations mapped to existing architecture

This specification does NOT define:

- How a Capability executes
- What language, runtime, or platform is used
- How Provenance is stored
- How Policy is represented internally
- What transport protocol is used between components

Those are implementation choices. This specification constrains what properties those choices MUST satisfy.

---

## 3. The Nine Primitives

The architecture defines exactly nine primitives arranged in a 3×3 grid across three concerns. This set is closed. A conformant implementation MUST NOT introduce additional primitives.

### 3.1 Context — Where Things Live

| Primitive | Specification |
|-----------|---------------|
| **Scope** | A bounded organizational or ownership context. Every other primitive instance MUST declare its Scope. Scopes MAY be nested. |
| **Domain** | An outcome-based accountability boundary for a functional area. A Domain MUST own at least one Capability. A Domain MUST declare its Scope. |
| **Capability** | The atomic unit of value, defined by the outcome it delivers. The locus of logic. A Capability MUST belong to exactly one Domain. A Capability MUST declare its Scope. |

### 3.2 Control — How It Is Constrained

| Primitive | Specification |
|-----------|---------------|
| **View** | A filtered projection of one or more Capabilities for a specific caller or use. A View MUST reference at least one Capability. A Capability MUST NOT reference a View. A View MUST declare its Scope. |
| **Archetype** | A caller role definition that constrains what actions a caller may take. Not a pattern or template. An Archetype MUST declare its Scope. |
| **Policy** | An enforced rule or constraint governing one or more primitives. Policy is authoritative: deny by default. A Policy MUST declare its Scope and MUST declare the primitive or primitives to which it attaches. Policies MAY attach to Scope, Archetype, Capability, Connector, or Binding. |

### 3.3 Interaction — How It Crosses Boundaries

| Primitive | Specification |
|-----------|---------------|
| **Connector** | A governed read access pathway across Scopes or Domains. A Connector MUST declare its Scope, the source it reads from, and the Policy or Policies that govern it. |
| **Binding** | A governed write access link that commits effects. A Binding MUST declare its Scope, the target it writes to, and the Policy or Policies that govern it. |
| **Provenance** | An immutable, append-only record of origin, change history, and ownership. Provenance MUST be emitted for all significant actions. Provenance is non-optional. |

### 3.4 Completeness

These nine primitives are the complete set. An implementation that requires a construct that cannot be expressed as an instance of, or relationship between, these primitives is outside the scope of this architecture.

---

## 4. The Declaration

### 4.1 Definition

The Declaration is the versioned, validated, diffable representation of all primitive instances and their relationships within a governed system. It is the single source of authority for the system's intended state.

### 4.2 Integrity Requirements

The Declaration is the root of trust for the entire architecture. If the Declaration can be silently modified, all downstream guarantees are void.

**DEC-1.** The Declaration MUST be integrity-protected. Any modification to the Declaration MUST be detectable.

**DEC-2.** The Declaration MUST be version-authenticated. Each version of the Declaration MUST be attributable to the caller that produced it.

**DEC-3.** Any modification to the Declaration MUST itself emit a Provenance event. The system's authority document is subject to its own governance.

**DEC-4.** The internal representation of the Declaration is not specified by this architecture. Implementations MAY use JSON, YAML, a database, a configuration DSL, or any other form, provided the above requirements are satisfied.

### 4.3 Authority

When drift is detected between runtime behavior and the Declaration, the Declaration is authoritative. When ambiguity arises in the interpretation of primitive relationships, the Declaration resolves it.

---

## 5. Runtime Resolution

When a session begins or an action is initiated, the system MUST resolve the architectural context before proceeding. The resolved context MUST be expressed entirely in primitives.

A resolved context MUST include:

- The active Scope
- The caller's Archetype
- The Capabilities available to that Archetype within that Scope
- For each available Capability: the Domain it belongs to, the Connectors it may read, the Bindings it may write, and the Policies that constrain it
- The Views available to that Archetype within that Scope

A resolved context MUST NOT include implementation details such as skill identifiers, engine configurations, model names, or runtime parameters. Those are resolved at compile time, not exposed at runtime. The architecture speaks only in primitives.

---

## 6. Invariants

The following eight invariants MUST hold in all conformant implementations. There are no exceptions.

### Invariant 1: Deny by Default

If no Policy explicitly allows an action, the action MUST be denied.

An action is allowed only if at least one Policy explicitly allows it. Any explicit deny MUST override any allow. Policies MAY attach to Scope, Archetype, Capability, Connector, or Binding. The absence of an applicable Policy is equivalent to an explicit deny.

### Invariant 2: Fail-Closed Execution

If the system that evaluates Policy is unavailable, or if Policy cannot be evaluated for any reason, the action MUST be denied.

If the architecture cannot resolve the context for an action — Scope, Archetype, applicable Policies — the action MUST NOT proceed. This invariant makes the failure mode of Invariant 1 explicit: deny by default includes the case where the default cannot be computed.

### Invariant 3: Single Source of Truth

Logic MUST live in Capabilities. Views adapt but MUST NOT duplicate logic. A change to the logic of a Capability MUST be made in the Capability itself, never in a View.

### Invariant 4: Scope Anchoring

Every primitive instance except Scope itself MUST declare its Scope.

### Invariant 5: Domain Ownership

Every Capability MUST belong to exactly one Domain. Every Domain MUST own at least one Capability. These constraints MUST hold at all times — a Capability without a Domain or a Domain without a Capability is a conformance violation, not a temporary state.

### Invariant 6: Provenance Required

All significant actions MUST emit Provenance events. A significant action is defined as: any Capability invocation, Connector read, Binding write, Policy evaluation resulting in deny, or modification to the Declaration.

### Invariant 7: Provenance Integrity

Provenance MUST be tamper-evident. Any modification to or deletion of a Provenance record MUST be detectable. This specification does not mandate a mechanism — hash chains, signed logs, immutable storage, or other approaches are all acceptable — but the property MUST hold. Provenance that can be silently altered provides no guarantee.

### Invariant 8: View Direction

Views MUST reference Capabilities. Capabilities MUST NOT reference Views. This dependency direction is strict and unidirectional.

---

## 7. Enforcement Boundary

The architecture separates concerns from mechanisms. However, certain enforcement points MUST be architecturally mandated even though the enforcement mechanism is implementation-chosen. An implementation that skips any of these enforcement points — including for performance reasons — is non-conformant.

### 7.1 Pre-Action Resolution

Before any Capability is invoked, Connector is read, or Binding is written, the architectural context MUST be resolved. This means the Scope, Archetype, and all applicable Policies MUST be identified and available before the action proceeds.

If pre-action resolution fails, the action MUST be denied per Invariant 2 (Fail-Closed Execution).

### 7.2 Policy Evaluation

Policy MUST be evaluated at every declared evaluation point. An implementation MUST NOT skip Policy evaluation for any reason, including performance optimization, caching assumptions, or prior evaluation results, unless the Policy itself explicitly declares that cached evaluations are acceptable and specifies the conditions under which the cache is valid.

If Policy evaluation fails due to a system error (not a policy denial), the action MUST be denied and a Provenance event MUST be emitted with an error status.

### 7.3 Provenance Emission

Provenance events MUST be emitted for all significant actions as defined in Invariant 6. An implementation MUST NOT allow a significant action to complete without a corresponding Provenance event.

The handling of Provenance write failures is specified in Section 8 (Error Semantics).

---

## 8. Error Semantics

A protocol assumes nothing about good faith. The following failure modes MUST be handled as specified.

### 8.1 Policy Evaluation Failure

When Policy evaluation fails due to a system error — not a policy denial, but the inability to evaluate the policy at all — the action MUST be denied. A Provenance event MUST be emitted recording the failure, including: the action attempted, the Policy that could not be evaluated, the error condition, and the timestamp.

### 8.2 Provenance Write Failure

When a Provenance event cannot be written, the implementation MUST declare one of two strategies:

**Strategy A — Suspend.** The action MUST be suspended until Provenance can be written. The action does not complete.

**Strategy B — Fallback.** The action MAY proceed only if a fallback Provenance mechanism exists and is operational. The existence of the fallback, the conditions under which it activates, and the fact that it was used MUST themselves be recorded in Provenance once the primary mechanism recovers.

The implementation MUST declare which strategy it uses. This declaration is itself part of the Declaration and MUST be recorded. An implementation MUST NOT silently drop Provenance events.

### 8.3 Scope Resolution Failure

When the system cannot determine the Scope in which an action is occurring, the action MUST be denied. If the system cannot determine where an action is happening, it cannot determine what is permitted.

### 8.4 Archetype Resolution Failure

When the system cannot determine the Archetype of the caller, the action MUST be denied. An unknown caller has no permissions.

---

## 9. Temporal Properties

Time is not a primitive. It is a dimension that primitives exist within. The architecture makes room for temporality without being defined by it.

**TEMP-1.** Policies, Archetypes, and Bindings MAY declare temporal constraints. These include but are not limited to: expiration times, validity windows, timeout durations, review intervals, and rotation periods.

**TEMP-2.** Implementations MUST honor all declared temporal constraints. A Policy that has expired MUST NOT be evaluated as active. An Archetype assignment that has expired MUST NOT grant permissions. A Binding with an elapsed timeout MUST NOT commit effects.

**TEMP-3.** The expiration or activation of a temporal constraint is a significant action and MUST emit a Provenance event.

**TEMP-4.** This specification does not require temporal constraints. It requires that when they are declared, they are enforced.

---

## 10. Conformance

### 10.1 Core Conformance

An implementation achieves Core Conformance if it satisfies ALL of the following:

1. All governed operations are expressed using the nine primitives defined in Section 3.
2. No additional primitives are introduced.
3. All eight invariants defined in Section 6 are satisfied.
4. Architectural context is resolved before action per Section 7.1.
5. Policy is evaluated at all declared evaluation points per Section 7.2.
6. Provenance is emitted for all significant actions per Section 7.3.
7. Error semantics are handled per Section 8.
8. Declared temporal constraints are honored per Section 9.
9. The system fails closed when resolution is impossible per Invariant 2.

Core Conformance is the minimum requirement for a conformant implementation. It is designed to be achievable by any implementor — from a solo builder managing personal agents to an enterprise deploying at scale.

### 10.2 Verifiable Conformance

An implementation achieves Verifiable Conformance if it satisfies ALL Core Conformance requirements AND all of the following:

1. Provenance is tamper-evident per Invariant 7, using a mechanism that is independently verifiable.
2. The Declaration is integrity-protected and version-authenticated per Section 4.2, using a mechanism that is independently verifiable.
3. All enforcement points defined in Section 7 are independently auditable — meaning a third party can verify that Policy was actually evaluated, that Provenance was actually emitted, and that pre-action resolution actually occurred. The system's claim that it enforced the architecture MUST be verifiable without relying solely on the system's own reporting.

Verifiable Conformance is RECOMMENDED for any deployment where multiple parties rely on the architecture's guarantees, where regulatory or compliance requirements apply, or where the consequences of non-conformance are significant.

### 10.3 Conformance Claims

An implementation that claims conformance MUST state which level it satisfies. An implementation MUST NOT claim Verifiable Conformance if it satisfies only Core Conformance requirements.

---

## 11. Security Considerations

This section identifies the primary threat vectors against the architecture and maps each to the mitigation provided by the existing primitive and invariant structure. No new primitives or requirements are introduced in this section.

### 11.1 Declaration Tampering

**Threat.** If the Declaration is modified without detection, all architectural guarantees are void. An attacker who controls the Declaration controls the system's definition of what is permitted.

**Mitigation.** Declaration integrity protection (Section 4.2, requirements DEC-1 through DEC-3). Under Verifiable Conformance, this protection is independently verifiable.

### 11.2 Policy Bypass

**Threat.** If an implementation allows actions without Policy evaluation, deny-by-default is meaningless. This includes both intentional bypass (for performance) and accidental bypass (through implementation error).

**Mitigation.** Mandatory Policy evaluation at all enforcement points (Section 7.2). Fail-closed execution when Policy cannot be evaluated (Invariant 2). Under Verifiable Conformance, Policy evaluation is independently auditable.

### 11.3 Provenance Forgery

**Threat.** If Provenance can be fabricated, back-dated, or selectively omitted, the audit trail is unreliable. The system loses its memory of what actually happened.

**Mitigation.** Provenance integrity requirement (Invariant 7). Provenance write failure handling (Section 8.2). Under Verifiable Conformance, tamper-evidence is independently verifiable.

### 11.4 Scope Leakage

**Threat.** If primitive instances are accessible outside their declared Scope, isolation is broken. A Capability, Connector, or Binding operating outside its declared Scope may access data or produce effects that violate the system's intent.

**Mitigation.** Scope Anchoring (Invariant 4). Pre-action Scope resolution (Section 7.1). Scope resolution failure handling (Section 8.3).

### 11.5 Archetype Elevation

**Threat.** If a caller can assume an Archetype they were not assigned, all permission boundaries are meaningless. This is the architectural equivalent of privilege escalation.

**Mitigation.** Pre-action Archetype resolution (Section 7.1). Archetype resolution failure handling (Section 8.4). Archetype assignment is a significant action and MUST emit Provenance (Invariant 6), making elevation attempts detectable.

---

## 12. Relationship to the Architectural Declaration

This specification and the Architectural Declaration (the Manifesto) are companion documents.

The Manifesto defines the architectural philosophy: why the architecture exists, what problem it solves, and the design principles that govern it. It speaks to builders and thinkers.

This specification defines the normative requirements: what an implementation MUST do to conform, what properties MUST hold, and what failure modes MUST be handled. It speaks to implementors and auditors.

Where the Manifesto uses natural language to establish intent, this specification uses normative language to establish obligation. Where the two documents address the same concept, this specification is authoritative for implementation requirements. The Manifesto is authoritative for design rationale and architectural philosophy.

Neither document specifies implementation mechanism. Both documents are subject to versioning and Provenance.

---

## 13. References

- *The Missing Layer: Architectural Declaration.* The companion manifesto defining the architectural philosophy.
- RFC 2119. *Key words for use in RFCs to Indicate Requirement Levels.* Bradner, S. March 1997.

---

## Appendix A: Primitive Relationship Summary

The following relationships are normative:

- Scope contains all other primitive instances (Invariant 4)
- Domain owns Capability (one-to-many, Invariant 5)
- Capability is the target of View (one-to-many, Invariant 8)
- Archetype constrains Capability invocation (many-to-many, governed by Policy)
- Policy attaches to Scope, Archetype, Capability, Connector, or Binding (many-to-many)
- Connector provides governed read access across Scope or Domain boundaries
- Binding provides governed write access that commits effects
- Provenance records all significant actions across all primitives (Invariant 6)
- View references Capability; Capability never references View (Invariant 8, strict unidirectional)

---

## Appendix B: Summary of Normative Requirements

| Requirement | Section | Conformance Level |
|---|---|---|
| Nine primitives, no additions | §3 | Core |
| Declaration integrity-protected | §4.2 DEC-1 | Core |
| Declaration version-authenticated | §4.2 DEC-2 | Core |
| Declaration changes emit Provenance | §4.2 DEC-3 | Core |
| Pre-action context resolution | §7.1 | Core |
| Mandatory Policy evaluation | §7.2 | Core |
| Mandatory Provenance emission | §7.3 | Core |
| Deny by default | §6 Inv-1 | Core |
| Fail-closed execution | §6 Inv-2 | Core |
| Single source of truth | §6 Inv-3 | Core |
| Scope anchoring | §6 Inv-4 | Core |
| Domain ownership | §6 Inv-5 | Core |
| Provenance required | §6 Inv-6 | Core |
| Provenance tamper-evident | §6 Inv-7 | Core |
| View direction | §6 Inv-8 | Core |
| Error handling: Policy failure | §8.1 | Core |
| Error handling: Provenance failure | §8.2 | Core |
| Error handling: Scope failure | §8.3 | Core |
| Error handling: Archetype failure | §8.4 | Core |
| Temporal constraints honored | §9 | Core |
| Provenance independently verifiable | §10.2 | Verifiable |
| Declaration independently verifiable | §10.2 | Verifiable |
| Enforcement points independently auditable | §10.2 | Verifiable |
