# Architectural Conformance

## 1. Why conformance is part of healing

Self-adaptive systems evolve. As they evolve, implementation structure can drift from the intended feedback-loop architecture. Cognitive Healing therefore treats architectural conformance as a first-class validation boundary rather than a documentation concern.

The reference paper used here is:

**Daniel San Martín, Valter Vieira de Camargo, Guisella Angulo. _Architectural Conformance Checking for MAPE-K-based Self-Adaptive Systems_. arXiv:2401.16382v3, 11 Sep 2025.**

The paper defines Architectural Conformance Checking (ACC) as comparison between a **Planned Architecture (PA)** and the **Current Architecture (CA)**, and presents REMEDY with specification, architecture recovery and automated checking.

## 2. AllasCode conformance model

```text
Semantic Specification
        │
        ├────────────► Planned Architecture
        │
        └────────────► generated/runtime metadata
                              │
                              ▼
                      Current Architecture
                              │
                  ┌───────────┴───────────┐
                  ▼                       ▼
          Static Conformance      Runtime Conformance
```

The goal is **Continuous Architectural Conformance**.

## 3. Conformance classes

AllasCode checks five categories:

```text
Conformance
├── Existence
├── Structural
├── Communication
├── Domain
└── Behavioral / Temporal
```

### Existence
Required abstractions/artifacts exist.

### Structural
Composition, ownership and hierarchy match the intended architecture.

### Communication
Dependencies and information flow obey allowed relationships.

### Domain
AllasCode-specific rules hold, including A³, capability, healing and governance constraints.

### Behavioral / Temporal
Execution order, causal traceability, idempotency, evidence production and recovery semantics hold over time.

## 4. must-use / must-not-use

REMEDY explicitly supports `must-use` and `must-not-use` rules for MAPE-K abstractions. AllasCode adopts the same semantic idea as an architecture-level contract.

Examples:

```text
executor must-use actor
actor must-use atomic-action
planner must-not-use atomic-action
planner must-not-use production mutation
llm must-not-use production effector
runtime monitor must-use runtime observation source
execution-agent must-use sandbox before promotion
```

A rule can represent direct or indirect dependency constraints.

## 5. Planned Architecture

The PA SHOULD be derivable from semantic definitions, not hand-maintained diagrams alone.

Example logical structure:

```text
ManagingSubsystem CognitiveHealing
├── Monitor RuntimeMonitor
├── Analyzer HealingAnalyzer
├── Planner CodeManagerAgent
├── Executor ExecutionAgent
└── Knowledge CognitiveHealingKnowledge

ManagedSubsystem AllasCodeRuntime
├── Sensors
│   ├── EventStream
│   ├── SchedulerTelemetry
│   ├── Metrics
│   ├── Traces
│   └── Logs
└── Effectors
    ├── SandboxMutation
    ├── RecoveryAction
    └── GovernedPromotion
```

## 6. Current Architecture recovery

Unlike approaches that depend heavily on manual source-to-architecture mapping, AllasCode SHOULD emit architecture metadata from the same semantic definitions used to generate/configure runtime artifacts.

Potential CA evidence sources:

- semantic manifests;
- compiled/generated metadata;
- module dependency graph;
- runtime capability graph;
- traces of actual calls/effects;
- Git changes;
- runtime event topology.

This reduces mapping subjectivity and lets the same model support both static and runtime checks.

## 7. Drift as healing input

An architectural drift is itself a diagnosable condition.

Example:

```text
Expected:
Planner → RepairProposal → ExecutionAgent → Actor/Action

Observed:
Planner → AtomicAction
```

This is not merely a style violation. It bypasses the intended proof/governance/execution boundary and therefore becomes a healing event.

Conceptually:

```text
ArchitecturalDrift
→ Observation
→ Analyzer
→ Repair Plan
→ Conformance Validation
```

## 8. Paper alignment

The paper reports that REMEDY:

- uses MAPE-K-specific abstractions in a DSL;
- recovers implementation artifacts into architectural representations;
- checks PA versus CA;
- encodes domain-specific communication constraints;
- detects architectural drift;
- supports hierarchical feedback loops;
- reduces specification effort and errors relative to a general-purpose DSL in its evaluation.

AllasCode reuses that foundation but adds:

- `Intent` as explicit adaptation-goal anchor;
- behavioral/temporal conformance;
- runtime event/effect evidence;
- predictive drift/failure analysis;
- causal experimentation;
- Git provenance;
- negative knowledge and executable regression constraints.

Reference: https://arxiv.org/abs/2401.16382
