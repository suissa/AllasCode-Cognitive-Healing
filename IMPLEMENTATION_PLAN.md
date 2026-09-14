# Implementation Plan

## Goal

Implement AllasCode Cognitive Healing incrementally without collapsing reasoning, execution and authorization into one agent.

The first milestone should prove the loop with deterministic contracts before introducing autonomous code mutation.

## Phase 0 — Contracts and schemas

Define stable schemas/types for:

- `Observation`
- `Diagnosis`
- `FailureEpisode`
- `RepairProposal`
- `RepairAttempt`
- `RepairExecutionResult`
- `MetricSnapshot`
- `LogObservation`
- `TraceReference`
- `CausalEvidence`
- `CausalExperiment`
- `LastKnownGoodState`
- `TemporalRecoveryPlan`

Acceptance:

- schemas are versioned;
- every record has correlation/provenance IDs;
- invariants in `docs/INVARIANTS.md` have machine-checkable mappings where practical.

## Phase 1 — Runtime Monitor

Implement Monitor directly in the AllasCode runtime.

Responsibilities:

- subscribe to runtime outcome/event boundaries;
- observe scheduler/resource state;
- correlate Agent/Actor/Action/Intent/Event IDs;
- reference logs/traces/metrics;
- emit normalized `Observation` records;
- avoid diagnosis/repair logic.

Acceptance:

- deterministic event/observation tests;
- low-overhead benchmark;
- replayable observation fixtures;
- no direct dependency from Monitor to repair execution.

## Phase 2 — Knowledge adapters

Implement interfaces for:

- EventStore historical truth;
- vector retrieval;
- causal graph;
- observability references;
- Git provenance.

Acceptance:

- one `FailureEpisode` can be reconstructed from IDs;
- successful and failed repairs are queryable separately;
- vector and graph retrieval are independently testable.

## Phase 3 — Analyzer v1 (reactive)

Implement deterministic/reactive analysis first:

- classify active failure;
- retrieve similar episodes;
- retrieve known-good and known-bad repairs;
- correlate observability;
- emit `Diagnosis`.

Acceptance:

- no LLM is required for the basic failure path;
- known-bad repair evidence is returned explicitly;
- Analyzer never applies effects.

## Phase 4 — CodeManagerAgent v1

Implement planning and LLM mediation:

- negative-knowledge gate;
- strategy ranking;
- bounded Knowledge projection;
- prompt construction;
- LLM response schema validation;
- invariant/conformance pre-check;
- `RepairProposal` output.

Acceptance:

- known-invalid proposal can be rejected without sandbox execution;
- CodeManagerAgent has no mutation capability;
- prompt/evidence projection is recorded for provenance.

## Phase 5 — ExecutionAgent + sandbox

Implement separate execution authority:

- isolated clone/worktree;
- commit each mutation attempt before validation;
- support empty evidence commits for failed validation;
- build/test execution;
- return structured `RepairExecutionResult`.

Acceptance:

- a failed repair remains addressable by Git SHA;
- validation evidence references exact repair SHA;
- no direct production mutation capability by default.

## Phase 6 — Negative Knowledge → executable constraints

For reproducible failures:

- generate regression test/constraint;
- commit it;
- associate it with `FailureEpisode`;
- include it in future validation.

Acceptance:

- a known bad behavior cannot regress silently;
- context-specific failures are not converted into unjustified global bans.

## Phase 7 — Architectural Conformance

Implement Planned Architecture / Current Architecture comparison inspired by REMEDY.

Start with rules:

```text
planner must-not-mutate production
planner must-not-use atomic-action directly
llm must-not-use production effectors
execution-agent must-use sandbox before promotion
actor must-use atomic-action for A³ effects
```

Acceptance:

- static violation fixtures detected in CI;
- runtime violations can be emitted as architectural-drift observations;
- domain rules are versioned.

## Phase 8 — Causal-temporal topology

Implement graph model and evidence levels:

```text
precedes
correlated_with
candidate_cause
increases_risk_of
intervention_supported
causal_supported
```

Add Observability nodes:

- `MetricSnapshot`
- `Trace`
- `LogObservation`

Acceptance:

- temporal precedence cannot automatically produce a causal edge;
- graph can answer precursor, consequence and repair-outcome queries;
- raw telemetry stays outside the graph with references.

## Phase 9 — Predictive Analyzer

Implement precursor detection and risk scoring.

Acceptance:

- risk output contains evidence and confidence;
- prediction does not authorize action;
- false-positive/false-negative metrics are measurable;
- compare graph+observability against vector-only baseline.

## Phase 10 — Causal sandbox experimentation

Implement controlled replay and intervention:

- reconstruct pre-failure state;
- treatment/control trials;
- fault injection;
- counterexample retention;
- causal evidence update.

Acceptance:

- experiment records are reproducible;
- treatment/control environment hashes are recorded;
- causal confidence changes only through explicit evidence policy.

## Phase 11 — Temporal recovery

Implement:

- Repair Forward;
- Saga compensation;
- Restore to `LastKnownGoodState`;
- Event replay.

Acceptance:

- recovery never deletes Event Sourcing history;
- every LastKnownGoodState has validation evidence;
- recovery strategy is selected through policy/risk evidence.

## Phase 12 — Governed promotion

Implement:

```text
Sandbox Verified
→ Governor
→ Clone/Staging
→ Canary
→ Production
```

Acceptance:

- sandbox success cannot bypass promotion policy;
- promotion and rollback are auditable events.

## Phase 13 — Spec-driven bridge

Define `AllasCode IR` and adapters from SDD sources.

Initial IR:

- contexts;
- entities/properties/relations;
- intents;
- behaviors;
- flows/events;
- invariants/constraints/policies;
- capabilities;
- data/observability requirements;
- acceptance criteria/proof obligations.

Acceptance:

- at least one external spec format compiles into IR;
- IR resolves one end-to-end canonical behavior without LLM-generated implementation code.

## Phase 14 — Canonical behavior registry

Implement evidence-backed canonical resolution.

Acceptance:

- semantic lookup resolves canonical behavior + version;
- each canonical implementation has explicit evidence/constraints;
- challenger implementation can supersede canonical version only after comparative validation.

## Research evaluation

Create reproducible experiments for these hypotheses:

1. negative knowledge reduces repeated failed attempts;
2. vector + causal graph outperforms vector-only repair retrieval;
3. predictive topology reduces realized failures;
4. causal interventions improve root-cause ranking;
5. Event-Sourced replay improves reproducibility;
6. conformance checks prevent planner/executor boundary erosion;
7. semantic/canonical reuse reduces generated code and token cost;
8. failure-to-test conversion reduces recurrence of known failures.

## Paper grounding

Architecture-conformance phases are inspired by REMEDY from:

Daniel San Martín, Valter Vieira de Camargo, Guisella Angulo. _Architectural Conformance Checking for MAPE-K-based Self-Adaptive Systems_. arXiv:2401.16382v3 (2025).

REMEDY provides the PA/CA/conformance foundation, MAPE-K abstractions, domain communication constraints and hierarchical feedback-loop framing. AllasCode extends these with Intent-governed adaptation, Event Sourcing, causal/predictive healing, negative knowledge, provenance and governed code-repair execution.
