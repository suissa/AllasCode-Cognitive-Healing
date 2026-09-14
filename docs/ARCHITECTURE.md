# Architecture

## 1. Purpose

AllasCode Cognitive Healing is the adaptive-control architecture responsible for observing the runtime, diagnosing current and predicted failures, planning constrained repairs, executing them in isolated environments, validating them, and promoting only verified changes.

It is deliberately decomposed so that observation, reasoning, planning, execution and authorization do not collapse into a single AI agent.

## 2. System boundary

MAPE-K wraps the managed runtime as a feedback loop:

```text
                        ┌──────────── Knowledge ─────────────┐
                        │                                    │
                        ▼                                    │
Managed Runtime → Monitor → Analyzer → CodeManagerAgent      │
      ▲                                  │                  │
      │                                  ▼                  │
      │                            RepairProposal            │
      │                                  │                  │
      │                                  ▼                  │
      │                           ExecutionAgent             │
      │                                  │                  │
      │                                  ▼                  │
      │                           Sandbox / Clone            │
      │                                  │                  │
      │                                  ▼                  │
      └──── Promotion ← Governor ← Proof / Validation ──────┘
```

The normal AllasCode runtime pipeline remains independent. Cognitive Healing observes and adapts it; it does not replace it.

## 3. Runtime Monitor

The Monitor belongs directly in the runtime because the runtime already owns the most authoritative execution context: event emission, Action/Actor outcomes, scheduler decisions, resource control and infrastructure signals.

The Monitor MUST observe facts, not infer diagnoses.

Responsibilities:

- observe `Ok` / `Error` runtime outcomes;
- capture canonical identifiers (`agent_id`, `actor_id`, `action_id`, `intent_id`, `event_id`);
- correlate `trace_id` / `span_id` where available;
- snapshot scheduler/resource state;
- reference logs, traces and metrics;
- emit typed `Observation` records;
- avoid embedding repair policy in the runtime.

Conceptual record:

```text
Observation
├── event_id
├── canonical_label
├── agent_id
├── actor_id
├── action_id
├── intent_id
├── timestamp
├── outcome
├── state_hash
├── scheduler_state
├── cpu
├── memory
├── latency
├── retries
├── trace_id
├── logs_ref
└── metrics_ref
```

## 4. Analyzer

The Analyzer interprets observations using the full Knowledge plane. It is responsible for both reactive diagnosis and predictive risk estimation.

```text
Analyzer
├── Semantic Retriever
├── Temporal Analyzer
├── Causal Analyzer
├── Observability Correlator
└── Predictive Risk Analyzer
```

Input:

```text
Observation + Knowledge
```

Output:

```text
Diagnosis
├── symptom
├── suspected_causes[]
├── causal_evidence[]
├── temporal_evidence[]
├── similar_failures[]
├── known_good_repairs[]
├── known_bad_repairs[]
├── violated_invariants[]
├── predicted_consequences[]
├── failure_risk
└── confidence
```

The Analyzer MUST NOT apply a repair.

## 5. CodeManagerAgent

The CodeManagerAgent is the planning and LLM-mediation boundary.

It:

1. receives a `Diagnosis`;
2. retrieves relevant successful and failed repair episodes;
3. excludes known-invalid, unsafe or causally incompatible approaches;
4. ranks remaining strategies;
5. decides whether an LLM call is necessary;
6. constructs a minimal, task-specific prompt from a bounded Knowledge projection;
7. validates the LLM response against schemas, invariants, negative knowledge and causal context;
8. emits a `RepairProposal`.

It MUST NOT modify code or production state.

A prompt projection SHOULD contain only what is needed:

```text
current problem
relevant causal chain
relevant temporal sequence
similar historical failures
repairs that worked
repairs that failed
forbidden approaches
relevant invariants
files/components in scope
expected validation criteria
```

## 6. ExecutionAgent

The ExecutionAgent is separate from CodeManagerAgent.

It receives an authorized `RepairProposal` and may:

- create/use a dedicated healing branch/worktree;
- apply a patch in a sandbox/clone;
- commit every mutation attempt;
- run build and test suites;
- run generated regression tests;
- run property/invariant tests;
- run chaos/fault-injection experiments when required;
- collect observability evidence;
- generate a `RepairExecutionResult`.

It has no authority to promote directly to production unless a specific Governor policy grants that capability.

## 7. Validation and promotion

Sandbox success is necessary but not sufficient.

```text
RepairProposal
→ Sandbox Execution
→ Build
→ Tests
→ Regression Constraints
→ Invariant Checks
→ Conformance Checks
→ Causal/Observability Checks
→ Proof/Evidence
→ Governor
→ Clone/Staging
→ Canary
→ Production
```

## 8. Reactive and predictive loops

### Reactive

```text
Failure
→ Observation
→ Diagnosis
→ RepairProposal
→ Execution
→ Validation
→ Recovery
```

### Predictive

```text
Precursor Pattern
→ Observation
→ Risk(Failure | Current Causal Topology)
→ Preventive Diagnosis
→ Preventive RepairProposal
→ Validation
→ Prevention
```

A predictive signal never authorizes an effect by itself.

## 9. Temporal recovery strategies

The Planner may select among:

- **Repair Forward** — move current state to a valid state by applying a new repair;
- **Compensate** — Saga-style semantic compensation through new actions/events;
- **Restore** — return to a previously validated snapshot/state boundary;
- **Replay** — reconstruct from Event Sourcing and continue from a known-good point.

No strategy deletes history.

## 10. Relationship to MAPE-K

The paper _Architectural Conformance Checking for MAPE-K-based Self-Adaptive Systems_ defines Monitor, Analyzer, Planner, Executor and shared Knowledge as the core feedback-loop abstractions and emphasizes communication rules between them. AllasCode preserves this separation, while mapping Planner to CodeManagerAgent and Execute to a separately authorized ExecutionAgent.

Reference: Daniel San Martín, Valter Vieira de Camargo, Guisella Angulo, arXiv:2401.16382v3 (2025).
