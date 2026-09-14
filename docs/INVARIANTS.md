# Invariants

This document defines architectural and behavioral invariants for AllasCode Cognitive Healing.

## MAPE-K separation

### INV-MAPEK-001 — Accepted-plan origin
Every execution MUST originate from an accepted repair or preventive plan.

### INV-MAPEK-002 — Causal traceability
Every plan MUST reference the Observation/Diagnosis that caused it.

### INV-MAPEK-003 — Planner purity
The Planner / CodeManagerAgent MUST NOT mutate runtime or production state.

### INV-MAPEK-004 — Execution boundary
Only an authorized execution component may request code/runtime side effects.

### INV-MAPEK-005 — A³ execution path
Runtime effects MUST use the authorized AllasCode Actor/AtomicAction path when the effect belongs to the A³ runtime.

### INV-MAPEK-006 — Evidence production
Every execution attempt MUST produce evidence, including failed attempts.

### INV-MAPEK-007 — Knowledge provenance
Authoritative Knowledge updates MUST be traceable to accepted events, observations, validation evidence or governed experiments.

### INV-MAPEK-008 — Idempotent repair execution
The same causal event/repair attempt identity MUST NOT execute the same effect twice unless the effect is explicitly idempotent and policy allows re-execution.

### INV-MAPEK-009 — No silent invariant violation
A violated invariant MUST NOT be silently ignored.

### INV-MAPEK-010 — Human escalation
An unresolved condition that exhausts valid healing alternatives MUST converge to Human-in-the-Healing-Loop rather than returning an ungoverned effect.

## Negative Knowledge

### INV-HEALING-NEGATIVE-001 — Failed repairs constrain future planning
A repair proven invalid or unsafe MUST influence future candidate selection.

### INV-HEALING-NEGATIVE-002 — Context-aware no-repeat
A known-invalid repair MUST NOT be executed again under an equivalent causal context unless new evidence justifies re-evaluation.

### INV-HEALING-NEGATIVE-003 — Executable failure knowledge
A repair proven invalid by reproducible evidence MUST produce an executable regression constraint whenever the failure condition can be deterministically tested.

### INV-HEALING-NEGATIVE-004 — No global ban from one contextual failure
A context-specific failure MUST NOT become a global prohibition without sufficient evidence.

## Syntropic learning

### INV-SYNTROPY-001 — Failure changes knowledge
A failed execution MUST NOT leave decision-relevant healing knowledge equivalent to its pre-execution state when new evidence was produced.

### INV-SYNTROPY-002 — Successful-repair explanation
Every resolved failure MUST preserve the evidence required to explain why the successful repair was preferred over relevant failed alternatives.

### INV-SYNTROPY-003 — Traceability over deletion
Knowledge may be superseded, but evidence lineage MUST NOT be destroyed merely because a newer conclusion is preferred.

## Causal reasoning

### INV-CAUSAL-001 — Precedence is not causation
Temporal precedence alone MUST NOT create a `causes` relationship.

### INV-CAUSAL-002 — Evidence-bearing causal edges
Every causal/risk edge MUST carry evidence metadata and confidence/evidence level.

### INV-CAUSAL-003 — Prediction is not authority
A predictive risk score MUST NOT directly authorize an effect.

### INV-CAUSAL-004 — Intervention traceability
Every sandbox causal experiment MUST preserve treatment/control configuration and outcome evidence.

### INV-CAUSAL-005 — Counterexamples retained
Counterexamples MUST be retained and influence confidence; causal support cannot be based only on confirming episodes.

## Observability

### INV-OBS-001 — Entity and system state are distinct
Entity/domain state MUST NOT be treated as a complete representation of system operational state.

### INV-OBS-002 — Correlation identifiers
Observability evidence SHOULD be correlatable through available IDs such as `trace_id`, `span_id`, `event_id`, `agent_id`, `actor_id`, `action_id`, `intent_id` and `entity_id`.

### INV-OBS-003 — No raw telemetry duplication requirement
The causal graph MUST NOT require duplication of all raw logs/traces/metrics; semantic observations and durable references are sufficient.

## Git provenance

### INV-PROV-001 — Code mutation provenance
Every code mutation performed by healing MUST have an immutable Git provenance reference.

### INV-PROV-002 — Failed repair addressability
Every failed repair MUST remain reproducible/addressable to the extent its environment and dependencies can be reconstructed.

### INV-PROV-003 — Commit before validation
Each code mutation attempt MUST be committed in the healing workspace before its validation result is finalized.

### INV-PROV-004 — Validation evidence linkage
Validation evidence MUST reference the exact repair commit it evaluates.

## Recovery

### INV-RECOVERY-001 — Rollback does not delete history
Compensation, restore or replay MUST NOT erase historical events.

### INV-RECOVERY-002 — LastKnownGood evidence
A `LastKnownGoodState` MUST be backed by validation evidence and a reconstructable snapshot/event boundary.

### INV-RECOVERY-003 — Governed promotion
A sandbox-verified repair MUST NOT automatically become a production repair without satisfying promotion policy.

## LLM boundary

### INV-LLM-001 — Proposal only
An LLM output is a proposal and MUST NOT directly mutate system state.

### INV-LLM-002 — Bounded knowledge projection
LLM context MUST be a bounded projection of Knowledge relevant to the repair task.

### INV-LLM-003 — Deterministic validation boundary
LLM-generated repair candidates MUST pass deterministic schema/policy/invariant gates before execution.

## Spec-driven generation

### INV-SPEC-001 — Semantic input boundary
Spec-driven integrations MUST compile into an explicit AllasCode semantic intermediate representation before implementation resolution.

### INV-SPEC-002 — Canonical resolution first
If a compatible canonical behavior exists, the system SHOULD resolve/reuse it before requesting arbitrary code generation.

### INV-SPEC-003 — Canonical is versioned evidence
A canonical implementation MUST be versioned and associated with explicit constraints and evidence; `canonical` MUST NOT imply permanent or universally optimal.
