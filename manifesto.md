# Architectural Declaration

*The architectural declaration of The Missing Layer.*

---

## Why This Exists

Anywhere AI moves faster than humans can synchronize, the same problem emerges.

People try to produce hard guarantees — scope, permissions, behavior, accountability — using soft tools: prose, prompts, and coordination.

At human speed, this works. At AI speed, it doesn't. Truth duplicates. Copies drift. Humans become the synchronization mechanism. No one can say with certainty what the system is supposed to do.

This problem is universal. It appears whether you're a solo builder managing five agents, a family coordinating life with AI assistance, a team aligning on shared tools, or an enterprise deploying at scale.

**Coherence means:** for any capability, at any moment, you can answer — what is it, who owns it, who can use it, what can it touch, what constrains it, and what happened. If any answer is unknown or contested, the system is incoherent.

This architecture exists to make those answers structural, not conversational.

---

## What This Is

The Missing Layer is the architectural substrate that enables coherence wherever meaning must survive faster-than-human iteration.

It is not a governance layer. It is not a policy framework. It is not an implementation specification.

It is architecture for coherence — the minimum viable structure required for intent to remain intelligible as complexity increases.

Architecture is what it is — the shapes, relationships, and invariants. Infrastructure is what it does — the substrate that products and systems are built on. Implementation is how it is realized — the skills, runtimes, and platforms that fill the shapes.

Same primitives. Same architecture. Different weight at each scale.

---

## The Nine Primitives

These are the irreducible shapes. Remove any one, and coherence becomes impossible.

Three concerns. A strict 3×3 reference grid with culminating primitives in the third column.

### Context — where things live

Ownership, meaning, and the unit of truth.

| Primitive | Purpose |
|-----------|---------|
| **Scope** | Bounded organizational or ownership context. The context boundary. |
| **Domain** | Outcome-based accountability for a functional area. Owns Capabilities. |
| **Capability** | Atomic unit of value, defined by the outcome it delivers. The locus of logic. Not a service. |

### Control — how it is constrained

How capabilities are exposed and constrained: projection, caller role, and enforcement.

| Primitive | Purpose |
|-----------|---------|
| **View** | Filtered projection of capabilities for a specific caller or use. |
| **Archetype** | Caller role definition that constrains what actions may be taken. Not a pattern or template. |
| **Policy** | Enforced rules and constraints governing primitives. Deny by default. |

### Interaction — how it crosses boundaries

How effects cross boundaries and persist: governed read, governed write, and immutable history.

| Primitive | Purpose |
|-----------|---------|
| **Connector** | Governed read access pathway across scopes or domains. |
| **Binding** | Governed write access link that commits effects. |
| **Provenance** | Immutable record of origin, change history, and ownership. Non-optional. |

**Immutable history applies to all primitives.**

Nothing else is a primitive.

**Term note — Caller:** A *caller* is a runtime identity — human or machine — operating within a Scope and assigned an Archetype.

**Term note — Capability:** A *Capability* is not a capability-based security token (an unforgeable reference granting access), nor a strategic planning construct (as in enterprise capability mapping). It is the architectural primitive that identifies a unit of value by the outcome it delivers. A Capability exists independently of whatever implements it.

Skills, agents, GPTs, workflows, dashboards, APIs — these are implementation details. They exist at a layer below the architecture. Implementors choose how to fill the primitive shapes. The architecture ensures those choices can compose.

---

## The Layer Distinction

| Architecture Layer | Implementation Layer |
|--------------------|----------------------|
| Defines the shapes | Fills the shapes |
| Primitives | Skills, MCP servers, LLMs, scripts |
| What "allowed" means | Whether something is allowed |
| Implementation-agnostic | Implementation-specific |
| This document | The Declaration |

**The Declaration** is a versioned, validated, diffable representation of primitive instances and their relationships. It is the authority for detecting drift and resolving ambiguity. Its internal form is not dictated by this architecture.

The architecture does not specify HOW a Capability executes. It specifies WHAT a Capability is, WHO can invoke it, WHAT it may read, WHAT it may write, and WHAT Policy constrains it.

An implementor might realize a Capability as:

- A data transformation
- An ML inference endpoint
- A content publishing operation
- A human workflow
- A personal automation
- A family coordination routine
- A cloud function

The architecture doesn't care. It ensures all of these can: anchor to a Scope, belong to a Domain, receive invocation from an Archetype, accept constraints from Policy, and emit Provenance.

**A Capability is not a service.** A service is a runtime container. A Capability is the unit of truth — where logic belongs. A service may implement a Capability, but the Capability exists even when implemented by a spreadsheet and a human process. Don't confuse the shape with what fills it.

---

## Runtime Resolution

When a session begins, the system resolves the architectural context. This is the only information the architecture layer exposes:

```json
{
  "scope": "scope.family_household",
  "archetype": "archetype.parent",

  "capabilities": [
    {
      "id": "capability.schedule_coordination",
      "domain": "domain.household_operations",
      "connectors": ["connector.calendar", "connector.school_portal"],
      "bindings": ["binding.calendar_write"],
      "policies": ["policy.kid_safe_times"]
    }
  ],

  "views": [
    {
      "id": "view.schedule_kid_friendly",
      "capability": "capability.schedule_coordination",
      "scope": "scope.family_household"
    }
  ]
}
```

No Skills. No engines. No implementation details.

Those are resolved at compile time, not exposed at runtime. The architecture speaks only in primitives.

---

## The Invariants

These must hold. Always. Without exception.

**1. Deny by Default**
If no Policy explicitly allows, the action is denied.

Policy is authoritative. An action is allowed only if at least one Policy explicitly allows it. Any explicit deny overrides any allow. Policies may attach to Scope, Archetype, Capability, Connector, or Binding.

**2. Single Source of Truth**
Logic lives in Capabilities. Views adapt but do not duplicate.

**3. Scope Anchoring**
Every primitive instance (except Scope itself) declares its Scope.

**4. Domain Ownership**
Every Capability belongs to exactly one Domain. Every Domain owns at least one Capability.

**5. Provenance Required**
All significant actions emit Provenance events. Non-negotiable.

Example event:

```json
{
  "event": "capability.invoked",
  "timestamp": "2026-01-28T14:30:00Z",
  "capability": "capability.schedule_coordination",
  "archetype": "archetype.parent",
  "scope": "scope.family_household",
  "connectors_read": ["connector.calendar", "connector.school_portal"],
  "bindings_written": ["binding.calendar_write"],
  "policy_evaluated": "policy.kid_safe_times",
  "policy_result": "allowed",
  "outcome": "success",
  "correlation_id": "sess_abc123"
}
```

This is what "what actually happened" looks like. Without it, the system has no memory.

**6. View Direction**
Views reference Capabilities. Capabilities never reference Views.

---

## What This Enables

**Coherence at any scale** — because the shapes are fixed, composition is predictable.

**Implementation freedom** — because the architecture doesn't dictate HOW, only WHAT.

**Blast-radius visibility** — because every change can be traced through primitive relationships.

**Entropy resistance** — because truth is defined once in structure, not duplicated in prose.

**Human relief** — because people can stop carrying the system in their heads.

---

## What This Is Not

**Not governance** — governance implies external oversight: committees, approval workflows, human review loops. The Missing Layer is internal machine-readable structure. It makes governance *possible* (you can build approval workflows on top of it), but it doesn't require governance. A solo builder uses the same primitives as an enterprise compliance team — the architecture doesn't care who's watching.

**Not a product** — this is architecture that functions as infrastructure. Products are built on top of it.

**Not implementation** — Skills, agents, and runtimes implement the shapes. They are not the shapes.

**Not organization-specific** — the same architecture serves a solo builder, a family, a team, an enterprise.

**Not optional at scale** — without architectural coherence, entropy wins. Always.

---

## Conformance

An implementation conforms if it can be expressed entirely in these primitives, satisfies the invariants, and emits Provenance for significant actions.

Anything that cannot be expressed without inventing new primitives is out of bounds.

---

## The Lock

The Missing Layer is the architectural substrate for coherence wherever AI moves faster than humans can synchronize.

All implementation choices — Skills, MCP servers, platform mappings, Gateway schemas, compiled GPTs — must express these primitives without inventing new ones.

When drift is detected, the architecture is the authority. When ambiguity arises, this document resolves it. When someone asks "what is this system?" — this document is the answer.
