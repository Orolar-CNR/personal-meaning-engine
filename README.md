Personal Meaning Engine (PME)

«A privacy-first personal decision intelligence architecture for understanding context, evaluating meaning, supporting decisions, and learning from reflective human feedback.»

---

Definition

Personal Meaning Engine (PME) is a personal decision intelligence architecture designed to help a system understand the relationship between a person's current context, goals, values, constraints, preferences, and available capacity, and use that understanding to produce context-aware recommendations.

PME does not treat a recommendation as the final output of the system.

Instead, it establishes a continuous loop:

Personal Context
      ↓
Context Validation
      ↓
Meaning Evaluation
      ↓
Recommendation
      ↓
User Decision
      ↓
Reflection
      ↓
Evidence
      ↓
Personal Adaptation
      ↓
Future Decisions

The architecture is designed around four properties:

- Context-aware — decisions are evaluated against the user's actual situation.
- Evidence-based — behavioral feedback does not automatically become a permanent preference.
- Reversible — important model and state changes are versioned and recoverable.
- Privacy-first — personal data is governed throughout its entire lifecycle.

PME is therefore not intended to be a generic recommendation engine. It is an architecture for personalized meaning-aware decision support.

---

Core Objective

The primary objective of PME is:

«To help a system make better decisions for a person by understanding what matters to that person in the context in which the decision is being made, while preserving user control over personal data and preventing unsupported behavioral inference.»

The system should distinguish between:

What the user said
What the system observed
What the system inferred
What the user explicitly corrected
What the system learned

These categories must never be treated as equivalent.

---

Architectural Principles

PME is governed by a set of architectural invariants.

These are not optional implementation guidelines. They define the semantic boundaries of the system.

1. Store Boundary

"Personal Context Store" and "Reflection Event Store" MUST remain separate.

Personal Context Store

Stores the contextual information used to understand a situation.

Examples:

- Goals
- Values
- Constraints
- Preferences
- Energy state
- Emotional state
- Time context
- Calendar context
- Relevant situational information

Context may be temporary, sensitive, stale, incomplete, or user-editable.

Reflection Event Store

Stores evidence about what happened after a recommendation was produced.

Examples:

- Accepted
- Rejected
- Modified
- Deferred
- Ignored
- Outcome
- Reason code
- Feedback confidence
- User correction
- Learning permission

Reflection events should be treated as an append-oriented evidence record.

A reflection event MUST NOT become a copy of the complete personal context.

---

2. Evidence-Based Adaptation

PME MUST NOT interpret every user rejection as proof that the underlying model is wrong.

A rejection may represent:

- Temporary circumstances
- Missing context
- Stale context
- External constraints
- A changed goal
- A new preference
- A safety concern
- An intentional user choice
- An actual model mismatch

Therefore:

Rejection
   ↓
Reason Classification
   ↓
Evidence Aggregation
   ↓
Adaptation Hypothesis
   ↓
Policy Validation
   ↓
Small Reversible Update

NOT:

Rejection
   ↓
Immediate Weight Change

---

3. Explicit Correction Has Precedence Over Inference

PME distinguishes between:

Observed Behavior

and:

Explicit User Correction

An explicit statement such as:

«"My health is more important than productivity right now."»

is stronger evidence than repeatedly observing a behavior that might imply the same preference.

Explicit user intent may therefore trigger a dedicated preference or state update flow, subject to validation and commit rules.

Behavioral inference must never silently override an explicit user instruction.

---

4. Reversible Learning

Every meaningful personalization update MUST be:

- Versioned
- Auditable
- Traceable to evidence
- Associated with a policy version
- Reversible

A weight update should be traceable through a chain such as:

User Decision
      ↓
Reflection Event
      ↓
Evidence Set
      ↓
Adaptation Hypothesis
      ↓
Weight Update
      ↓
User Model Version

A system administrator or developer should be able to determine:

«Why did this weight change?»

and:

«Which evidence caused the change?»

---

5. Privacy as a Control Plane

Privacy is an architectural control layer, not merely documentation.

All operations involving personal data should be evaluated against policy before execution.

Examples:

CanCollect?
CanUse?
CanDerive?
CanReflect?
CanLearn?
CanRetain?
CanExport?
CanDelete?

A privacy decision should be treated as a prerequisite for data processing.

---

Context Processing

Context Lifecycle

Collection
    ↓
Validation
    ↓
Completeness
    ↓
Freshness
    ↓
Consistency
    ↓
Clarification / Normalization
    ↓
Decision Processing

PME MUST distinguish:

Missing       ≠ False
Unknown       ≠ Negative
Uncertain     ≠ Known
Stale         ≠ Current

For example, if the system does not know the user's energy level, it must not interpret that absence as low energy.

---

Clarification Gate

When required context is incomplete, PME should determine whether the missing information materially affects the decision.

Missing Context
      ↓
Impact Analysis
      ↓
┌─────────────────────────────┐
│ Low impact                  │
│ → Proceed with lower        │
│   confidence                │
└─────────────────────────────┘

┌─────────────────────────────┐
│ High impact                 │
│ → Ask clarification         │
└─────────────────────────────┘

Clarification should be:

- Minimal
- Relevant
- Easy to answer
- Context-specific
- Optional when appropriate

The user should be able to decline.

A declined clarification MUST NOT automatically become a value such as "low", "false", or "negative".

---

Clarification Budget

PME should enforce a bounded clarification process.

Example:

Maximum clarification questions per decision: 3

When the available information remains insufficient after the clarification budget is exhausted, the system should produce a lower-confidence decision rather than repeatedly interrogating the user.

---

Meaning Evaluation

The MeaningScore Engine evaluates how well an option fits the current personal context.

A conceptual model may include:

MeaningScore =
    w1 × Goal Alignment
  + w2 × Value Alignment
  + w3 × Energy Fit
  + w4 × Constraint Fit
  + w5 × Preference Fit
  + w6 × Context Relevance

The exact mathematical implementation is intentionally left open.

The architecture requires that:

1. Inputs are identifiable.
2. Weights are versioned.
3. Missing inputs are represented as unknown rather than fabricated.
4. Confidence is preserved.
5. Score changes are traceable.

---

Reflection Loop

Reflection is the mechanism through which PME learns from real user behavior.

Recommendation
      ↓
User Decision
      ↓
Outcome
      ↓
Reflection Event
      ↓
Reason Classification
      ↓
Evidence Aggregation
      ↓
Adaptation Hypothesis
      ↓
Policy Validation
      ↓
Weight Update
      ↓
New User Model Version

Reflection Outcomes

Supported outcome categories may include:

ACCEPTED
REJECTED
MODIFIED
DEFERRED
IGNORED
UNKNOWN

These outcomes are observations, not automatic interpretations.

---

Rejection Semantics

A rejection should carry semantic information where available.

User Feedback| Interpretation| Learning Action
"I was too tired."| Energy/context mismatch| Possible energy-fit evidence
"You did not know I had another commitment."| Missing or stale context| Improve context handling
"My priorities changed."| Explicit preference/goal change| Enter explicit update flow
"I chose something else."| Neutral behavioral observation| No automatic preference inference
"That recommendation was unsafe."| Safety failure| Enter safety/incident workflow

This separation prevents PME from changing the wrong layer of the architecture.

---

Evidence-Based Weight Updates

PME should treat learning as a progressive process.

Observation
    ↓
Evidence
    ↓
Pattern
    ↓
Hypothesis
    ↓
Repeated Confirmation
    ↓
Weight Update

Recommended guardrails include:

Minimum Evidence

A personalized update should normally require multiple sufficiently similar observations.

Example policy:

Minimum supporting events: 3–5

The exact threshold may be configurable.

Confidence Threshold

Learning should require sufficient confidence in both feedback and context.

Example:

Feedback confidence >= 0.70
Context completeness >= 0.70

These values are policy parameters, not universal constants.

Maximum Delta

A single learning operation should only change a weight by a bounded amount.

Example:

Maximum update delta: 0.02–0.05

Weights should be normalized after an update when required by the scoring model.

Decay

Old evidence should gradually lose influence as circumstances change.

This prevents historical behavior from permanently defining the user.

Revalidation

A previously learned pattern should periodically be checked against new evidence.

Rollback

A personalization update must be reversible.

---

User Model

PME should conceptually distinguish between three levels of information:

Global Model
      +
Personal Model
      +
Contextual Adaptation

This prevents a temporary personal state from contaminating a long-term profile and prevents a single user from implicitly changing the behavior of the global system.

---

Personal Weight Profile

A user-specific weight profile may conceptually look like:

{
  "profile_version": "uwp-v7",
  "weights": {
    "goal_alignment": 0.18,
    "value_alignment": 0.20,
    "energy_fit": 0.32,
    "constraint_fit": 0.25,
    "preference_fit": 0.15,
    "context_relevance": 0.24
  },
  "updated_from": "evidence-set-104",
  "policy_version": "policy-v3"
}

The exact schema and mathematical constraints are implementation-specific.

---

Data Architecture

PME should maintain explicit boundaries between:

Personal Context Store
        │
        │ context_snapshot_ref
        ▼
MeaningScore / Decision Layer
        │
        ▼
Recommendation
        │
        ▼
Reflection Event Store
        │
        ▼
Evidence Aggregator
        │
        ▼
Adaptation Layer
        │
        ▼
User Weight Profile Store

The three stores have different semantic responsibilities.

Store| Primary purpose| Lifecycle
Personal Context Store| Understand current circumstances| Mutable / expirable
Reflection Event Store| Preserve interaction evidence| Append-oriented
User Weight Profile Store| Store learned personalization| Versioned

---

Reflection Event Contract

A reflection event should contain only the information required for reflection, auditability, and learning.

Example:

{
  "event_id": "ref_01J...",
  "event_type": "recommendation_rejected",
  "occurred_at": "2026-09-01T16:45:00+07:00",

  "subject_ref": "user_pseudonymous_id",
  "decision_id": "dec_01J...",
  "recommendation_id": "rec_01J...",

  "context_snapshot_ref": "ctx_01J...",
  "context_schema_version": "v1",

  "context_completeness": 0.78,
  "context_freshness": "current",

  "meaning_score_version": "ms-v3",
  "weight_profile_version": "uwp-v7",

  "decision_outcome": "rejected",
  "reason_code": "energy_mismatch",
  "reason_text_ref": "encrypted_optional_reference",

  "feedback_confidence": 0.86,
  "learning_permission": true,

  "sensitive_context_used": true,
  "retention_scope": "reflection_standard"
}

The event should reference sensitive context rather than duplicate it.

---

Privacy Classification

PME should classify data according to sensitivity.

Level 0 — System Data

Examples:

- Request ID
- Application version
- Model version
- Error code
- Latency

Level 1 — General Personal Context

Examples:

- General preferences
- Non-sensitive goals
- Interaction settings

Level 2 — Behavioral Data

Examples:

- Recommendation history
- Accept/reject behavior
- Decision patterns
- Long-term behavioral signals

Level 3 — Highly Sensitive Personal Context

Examples:

- Emotional state
- Mood
- Energy
- Private reflections
- Journal content
- Sensitive life circumstances
- Free-form personal notes

Level 3 data requires the strongest access, retention, and processing controls.

---

Data Minimization

PME should never store personal information merely because it may become useful later.

Prefer:

Raw Personal Content
        ↓
Necessary Processing
        ↓
Minimal Derived Feature

rather than continuously retaining the complete raw content.

Derived information must still be treated as personal data when it can be linked back to an individual.

---

Encryption and Key Management

Sensitive data should be protected:

In Transit
At Rest
In Backup
During Export

Encryption keys must be managed separately from encrypted application data.

Conceptually:

Application
    ↓
Key Management Layer
    ↓
Encryption Key
    ↓
Encrypted Data Store

Key lifecycle should include:

Generate
→ Store
→ Rotate
→ Revoke
→ Destroy

---

Access Control

PME should use:

Default Deny
Least Privilege
Need to Know
Purpose Binding

A component should only receive the minimum information required for its function.

For example:

MeaningScore Engine
→ Derived context features

Reflection Engine
→ Outcome + reason + confidence

Analytics
→ Aggregated information

Operations
→ Sanitized technical logs

A system operator should not have routine access to private journal content.

---

Logging Policy

Personal content must not leak into operational logs.

Avoid:

ERROR:
User wrote: "..."

Prefer:

ERROR:
context_processing_failed
context_type=emotion
reason=validation_error
request_id=req_1029

Operational logging and personal content storage are separate concerns.

---

Consent and Purpose Separation

PME should distinguish processing purposes such as:

Core Decision Support
Personalization
Reflection Learning
Analytics
Research
Global Model Improvement

Permission for one purpose must not implicitly authorize all other purposes.

A user may be able to allow:

Personalization = enabled
Reflection Learning = enabled
Analytics = disabled
Global Training = disabled

---

Global Training Boundary

Personal user data should not automatically enter global model training.

The default architectural rule is:

PRIVATE USER DATA
        ↓
PERSONALIZATION ONLY

Moving information from personal processing into global model improvement requires a separate policy boundary and appropriate authorization.

---

User Data Rights and Controls

PME should provide user controls for:

View
Correct
Delete
Export
Disable Collection
Disable Reflection Learning
Reset Personal Model

Deletion must consider derived information.

For example:

Raw Context
   ↓
Derived Feature
   ↓
Reflection Event
   ↓
Evidence Set
   ↓
Weight Update

Deleting the raw context alone may not be sufficient if downstream artifacts remain attributable to that context.

The system should therefore support deletion propagation, invalidation, recomputation, or unlinking according to the applicable data policy.

---

Data Lifecycle

Every personal record should have lifecycle metadata.

Conceptually:

{
  "data_classification": "L3_highly_sensitive",
  "purpose": [
    "decision_support",
    "reflection_learning"
  ],
  "collection_method": "user_entered",
  "created_at": "2026-09-01T16:40:00+07:00",
  "expires_at": "2026-10-01T00:00:00+07:00",
  "retention_policy_version": "rp-v1",
  "deletion_status": "active"
}

A record should not be retained indefinitely by default.

---

Error Handling and Edge Cases

PME must fail safely when the information required for meaningful reasoning is unavailable.

Context Failure

Context unavailable
→ Do not fabricate values
→ Attempt recovery
→ Ask clarification when necessary
→ Lower confidence when appropriate

Stale Context

Old context
→ Detect age
→ Mark as stale
→ Refresh when decision impact is high

Conflicting Context

Conflicting signals
→ Do not silently choose one
→ Request clarification or reduce confidence

Clarification Failure

User does not answer
→ Continue with available information
→ Preserve uncertainty
→ Do not infer a missing value

Reflection Failure

If reflection persistence fails:

Decision completes
→ Reflection event remains pending
→ Retry / queue event
→ Never invent a synthetic event

Weight Update Failure

If an update cannot be safely committed:

Keep previous model version
→ Preserve evidence
→ Record failed adaptation
→ Retry through controlled process

The previous valid state remains authoritative until the new version is successfully committed.

---

Auditability

A core requirement of PME is traceability.

For an important recommendation, the system should be able to reconstruct:

What context was available?
       ↓
How complete was it?
       ↓
Which scoring model was used?
       ↓
Which weights were active?
       ↓
Why was the recommendation generated?
       ↓
What did the user do?
       ↓
What evidence was collected?
       ↓
Why did the system learn or not learn?
       ↓
Which model version resulted?

This is the foundation for trustworthy personalization.

---

Architectural Invariants

The following statements define PME at the architectural level:

1. Missing Context ≠ False Context
2. Unknown ≠ Negative
3. Uncertain ≠ Known
4. Rejection ≠ Model Error
5. Single Observation ≠ Stable Preference
6. Derived Data ≠ Non-Personal Data
7. Personal Data ≠ Global Training Data
8. Explicit User Correction > Behavioral Inference
9. No Evidence → No Personalized Weight Update
10. No Authorization → No Optional Processing
11. No Necessity → No Storage
12. No Valid Commit → No Model-State Change
13. Every Important Update Must Be Auditable
14. Every Personalized Update Must Be Reversible
15. Personal Context and Reflection Evidence Must Remain Separated

These invariants should be treated as architectural acceptance criteria for future implementations.

---

Relationship to NGCR

When PME operates alongside an NGCR-based runtime, PME should function as a specialized meaning, decision, and reflective adaptation layer.

Conceptually:

NGCR Runtime
├── Narrative State
├── Identity State
├── Goal State
├── Continuity State
└── Policy Layer
        │
        ▼
Personal Meaning Engine
├── Context Layer
├── MeaningScore Engine
├── Recommendation Layer
├── Reflection Layer
├── Evidence Aggregator
└── Personal Adaptation Layer
        │
        ▼
User Model

PME should not silently mutate identity, narrative, or other protected state merely because a recommendation was rejected.

Important state transitions should pass through the appropriate reflection and policy mechanisms.

---

Recommended Repository Structure

personal-meaning-engine/
│
├── README.md
│
├── docs/
│   ├── architecture/
│   ├── invariants/
│   ├── context/
│   ├── reflection/
│   ├── privacy/
│   ├── governance/
│   └── decision-model/
│
├── specifications/
│   ├── context-schema/
│   ├── reflection-event/
│   ├── meaning-score/
│   ├── weight-profile/
│   └── policy/
│
├── schemas/
│
├── policies/
│
├── examples/
│
└── tests/
    ├── invariants/
    ├── context/
    ├── reflection/
    ├── privacy/
    └── adaptation/

The architecture should remain implementation-agnostic until the underlying requirements and contracts are stable.

---

Recommended RFC Roadmap

The architecture can be formalized through focused RFCs:

RFC-0001 — PME Architecture and System Definition
RFC-0002 — Personal Context Model
RFC-0003 — Context Validation and Clarification
RFC-0004 — MeaningScore Model
RFC-0005 — Recommendation Contract
RFC-0006 — Personal Context Store Boundary
RFC-0007 — Reflection Event Contract
RFC-0008 — Evidence-Based Adaptation
RFC-0009 — User Weight Profile
RFC-0010 — Privacy and Data Lifecycle
RFC-0011 — Policy and Authorization
RFC-0012 — Auditability and Reversibility

---

Non-Goals

PME is not intended to:

- Diagnose medical or psychological conditions
- Determine a user's identity without explicit support
- Make irreversible decisions on behalf of the user
- Treat behavioral patterns as absolute truths
- Collect personal information without a defined purpose
- Use private user information for global model training by default
- Replace user agency with automated optimization

PME is a decision-support architecture, not an authority over the person it serves.

---

Design Philosophy

PME is based on a simple principle:

A person's decision should be interpreted within the context in which it occurs, and a person's behavior should not be mistaken for the complete definition of who they are.

The system should become more useful through evidence, not assumption.
It should become more personalized through reflection, not surveillance.
It should become more adaptive without becoming less controllable.

And it should preserve the user's ability to understand, correct, reset, and ultimately control what the system learns about them.

---

Project Status

Architecture Definition

This repository defines the conceptual architecture, contracts, invariants, and governance principles of the Personal Meaning Engine.

Implementation details, technology choices, deployment architecture, and production security controls should be defined in subsequent specifications after the core architectural contracts are stabilized.

---

License

To be defined.

---

Security

Security-sensitive implementation details should be documented separately from the public architectural specification.

For vulnerability reporting and security procedures, see the repository's future security policy.
