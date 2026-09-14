# AllasCode Cognitive Healing

AllasCode Cognitive Healing formalizes the self-adaptive, predictive, evidence-driven healing architecture of AllasCode.

The design uses **MAPE-K** as the feedback-loop foundation, but extends it with Event Sourcing, semantic/vector retrieval, causal-temporal topology, observability, proof/governance boundaries, Git provenance, sandbox experimentation, predictive self-healing, negative knowledge, and executable regression constraints.

> Core principle: **AI proposes semantics and repair candidates; deterministic AllasCode mechanisms decide whether they may become effects.**

## Architectural thesis

```text
Managed Runtime
   │
   ▼
Runtime Monitor
   │ observations
   ▼
Analyzer
   │ diagnosis + causal evidence + risk
   ▼
CodeManagerAgent
   │ constrained repair proposal
   ▼
ExecutionAgent
   │
   ▼
Sandbox / Clone
   │
   ▼
Validation + Proof + Governance
   │
   ▼
Promotion / Recovery
   │
   └──────────── evidence ────────────► Knowledge
```

The `CodeManagerAgent` **does not mutate production code**. It plans, retrieves evidence, eliminates known-bad strategies, builds an optimized prompt for an LLM when needed, and validates the returned proposal against Knowledge before execution.

The `ExecutionAgent` is the only agent authorized to apply a repair in an authorized environment, beginning with a sandbox/clone.

## MAPE-K mapping

| MAPE-K | AllasCode Cognitive Healing |
|---|---|
| Monitor | Runtime-native observation of events, success/error outcomes, scheduler, infrastructure and observability signals |
| Analyze | Diagnosis, semantic retrieval, causal/temporal analysis, predictive risk and failure classification |
| Plan | `CodeManagerAgent`: candidate selection, negative-knowledge filtering, LLM mediation and `RepairProposal` construction |
| Execute | `ExecutionAgent` in sandbox/clone, followed by validation and governed promotion |
| Knowledge | Event history, observability, healing episodes, causal graph, vector memory, Git provenance, invariants, tests and evidence |

MAPE-K is treated as a **feedback-control architecture around the runtime**, not as a linear replacement for the runtime pipeline.

## Core extensions

```text
MAPE-K
+ Event Sourcing
+ Observability
+ Semantic / Vector Retrieval
+ Causal-Temporal Graph
+ Predictive Analysis
+ Negative Knowledge
+ Executable Regression Constraints
+ Git Provenance
+ Sandbox Causal Experiments
+ Proof / Governor
= AllasCode Cognitive Healing
```

## Event Sourcing

MAPE-K itself does not prescribe Event Sourcing. AllasCode adds Event Sourcing as historical truth for the adaptive loop:

```text
MAPE-K + Event Sourcing
= adaptive loop with replayable history
```

Events are never erased to simulate rollback. Recovery produces new compensation/restore/replay evidence while preserving the original history.

## Knowledge model

```text
Knowledge
├── Runtime Knowledge
│   ├── EventStore
│   ├── current state
│   └── execution evidence
├── Observability Knowledge
│   ├── logs
│   ├── traces
│   ├── metrics
│   ├── profiles
│   ├── health checks
│   └── resource/scheduler telemetry
├── Healing Knowledge
│   ├── failure episodes
│   ├── successful repairs
│   ├── failed repairs
│   ├── rejected repairs
│   ├── unsafe repairs
│   └── generated regression constraints
├── Causal Knowledge
│   └── semantic temporal causal graph
└── Provenance Knowledge
    ├── Git commits
    ├── patches
    ├── validation commits
    ├── test evidence
    └── lineage
```

The LLM does **not** own Knowledge. It receives a bounded projection of Knowledge selected for the current task.

## Predictive self-healing

```text
Reactive Healing
Failure → Analyze → Plan → Validate → Heal

Predictive Healing
Precursor Pattern → Risk Estimation → Preventive Plan → Validate → Prevent
```

Prediction is evidence, never authorization:

```text
Prediction != Authorization
```

All preventive actions still pass through proof, policy and governance.

## Negative Knowledge and syntropic healing

A failed repair must improve the future search space:

```text
failure
→ evidence
→ new constraint
→ executable regression test when possible
→ smaller future search space
→ better next decision
```

> **Every failure must reduce future uncertainty.**

A known-invalid repair is not executed again in an equivalent causal context unless new evidence justifies re-evaluation.

When a failure is reproducible, it should become **executable knowledge** through unit, integration, property, invariant, chaos, load or conformance tests.

## Causal experimentation

Correlation is not automatically promoted to causation. The system records progressively stronger relations:

```text
precedes
correlated_with
candidate_cause
increases_risk_of
intervention_supported
causal_supported
```

Candidates can be tested in a clone by reconstructing the pre-failure state and performing controlled interventions/fault injection. Event Sourcing supplies replayable history; observability supplies operational evidence; sandbox experiments provide intervention evidence.

## Temporal recovery

Healing has four first-class strategies:

```text
Repair Forward
Compensate
Restore
Replay
```

The planner may choose a Saga-style compensation, restore to the last known good state, replay from a snapshot/event offset, or forward repair according to risk, cost and evidence.

Rollback never means deleting history.

## Git as healing provenance

Every repair mutation is committed in a healing branch/worktree. Failed attempts are preserved rather than disappearing from the learning history.

```text
repair commit
→ validation result/evidence
→ next attempt
→ final verified repair
```

An empty commit may record a validation failure when no additional file mutation is necessary. Git therefore becomes part of the provenance graph and a reconstruction source for Healing Knowledge.

## Spec-driven and canonical implementation direction

Long term, the LLM should generate semantic declarations rather than arbitrary implementation code:

```text
Spec
→ SpecNormalizer
→ SemanticExtractor
→ AllasCode IR
→ CanonicalResolver
→ ArchitectureCompiler
→ Runtime Artifacts
```

Canonical does **not** mean immutable. It means the best currently accepted implementation under explicit constraints and available evidence.

## Scientific foundation: MAPE-K and REMEDY

The principal paper grounding this formalization is:

**Daniel San Martín, Valter Vieira de Camargo, Guisella Angulo. _Architectural Conformance Checking for MAPE-K-based Self-Adaptive Systems_. arXiv:2401.16382v3, 11 Sep 2025.**

The paper formalizes MAPE-K as a reference architecture with Monitor, Analyzer, Planner, Executor and shared Knowledge, and presents **REMEDY**, a domain-specific Architectural Conformance Checking method with:

1. a DSL for Planned Architecture in MAPE-K terms;
2. recovery of the Current Architecture from implementation artifacts;
3. automated conformance checking for architectural drift;
4. domain-specific `must-use` / `must-not-use` communication rules;
5. hierarchical feedback-loop support.

AllasCode adopts these foundations and extends them with adaptation goals represented by Intent, Event-Sourced historical truth, predictive causal analysis, negative knowledge, executable failure constraints, proof/governance before effects, provenance, and causal sandbox experimentation.

The paper explicitly notes that REMEDY does not yet model adaptation goals and identifies predictive techniques for proactive drift detection as future work. AllasCode therefore treats **Intent** as the adaptation-goal anchor and predictive causal healing as a first-class extension.

Reference: https://arxiv.org/abs/2401.16382

## Documentation

- [Architecture](docs/ARCHITECTURE.md)
- [Knowledge Model](docs/KNOWLEDGE_MODEL.md)
- [Causal-Temporal Topology](docs/CAUSAL_TOPOLOGY.md)
- [Healing Protocol](docs/HEALING_PROTOCOL.md)
- [Invariants](docs/INVARIANTS.md)
- [Architectural Conformance](docs/CONFORMANCE.md)
- [Theoretical Foundation](docs/THEORETICAL_FOUNDATION.md)
- [Spec-Driven Integration](docs/SPEC_DRIVEN_INTEGRATION.md)
- [Implementation Plan](IMPLEMENTATION_PLAN.md)
