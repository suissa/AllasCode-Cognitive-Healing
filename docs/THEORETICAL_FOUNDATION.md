# Theoretical Foundation

## 1. Scope

AllasCode Cognitive Healing is grounded in self-adaptive systems, MAPE-K feedback loops, architectural conformance, Event Sourcing, causal/temporal reasoning, observability, sandbox experimentation and governed execution.

This document distinguishes what is directly grounded in the principal paper from AllasCode-specific extensions.

---

## 2. Principal paper

**San Martín, Daniel; Vieira de Camargo, Valter; Angulo, Guisella. _Architectural Conformance Checking for MAPE-K-based Self-Adaptive Systems_. arXiv:2401.16382v3, 11 Sep 2025.**

Reference: https://arxiv.org/abs/2401.16382

### 2.1 What the paper establishes

The paper treats MAPE-K as a reference architectural model for Self-Adaptive Systems (SAS), with five canonical abstractions:

```text
Monitor
Analyzer
Planner
Executor
Knowledge
```

The paper states that MAPE-K prescribes not only these abstractions but also expected communication/dependency rules between them.

It identifies architectural drift as divergence between intended architecture and implementation over system evolution. Drift may arise when implementation code for architectural abstractions spreads or when communication rules are violated.

Architectural Conformance Checking (ACC) is framed as a three-stage activity:

1. define a Planned Architecture (PA);
2. recover the Current Architecture (CA) from implementation artifacts;
3. compare PA and CA to identify mismatches.

### 2.2 REMEDY

The paper proposes **REMEDY**, a domain-specific ACC method for MAPE-K-based SASs.

REMEDY contributes:

- `DSL-REMEDY` for expressing Planned Architectures in MAPE-K terms;
- automated mapping/recovery of implementation artifacts to architectural elements;
- a conformance checking engine;
- domain-specific communication rules;
- visual representation of PA, CA and detected drift;
- support for hierarchical feedback loops.

The DSL includes communication constraints expressed as `must-use` and `must-not-use`. The paper also describes predefined MAPE-K domain rules, including forbidden interactions such as direct Planner↔Monitor access under the modeled rules.

### 2.3 Lower-level MAPE-K abstractions

The paper enriches the canonical MAPE-K representation with lower-level concepts drawn from prior work:

```text
Managing Subsystem
├── LoopManager
│   └── Loop
│       ├── Monitor
│       ├── Analyzer
│       ├── Planner
│       ├── Executor
│       └── Knowledge
│           ├── ReferenceInput
│           └── Alternative

Managed Subsystem
├── Sensor
├── MeasuredOutput
└── Effector
```

The paper describes:

- `Alternative` as a set of available options for changing system behavior;
- `ReferenceInput` as concrete desired values representing the state to be achieved/maintained;
- `MeasuredOutput` as observed values from the managed system, compared against Reference Inputs.

This directly motivates the AllasCode mapping:

```text
ReferenceInput  ≈ expected state / Intent / invariant expectation
MeasuredOutput  ≈ runtime/observability evidence
Alternative     ≈ candidate healing strategy
```

### 2.4 Self-adaptive goals

The paper reviews common self-adaptive goals including:

- self-configuring;
- self-optimizing;
- self-healing;
- self-protecting.

It also notes a limitation: REMEDY focuses on the feedback loop and does **not yet model adaptation goals**, while acknowledging that adaptation goals are important for runtime reasoning and decision-making.

AllasCode uses this gap as a natural integration point for **Intent**.

### 2.5 Predictive direction

The paper explicitly identifies integration of predictive techniques for proactive drift detection as future work.

AllasCode Cognitive Healing elevates predictive analysis to a first-class concern through causal-temporal topology, observability trajectories and controlled intervention evidence.

---

## 3. AllasCode extension: Intent-Governed MAPE-K

AllasCode does not replace MAPE-K. It specializes it.

```text
Intent
  ↓
Expected State / Constraints
  ↓
Monitor
  ↓
Analyze
  ↓
Alternatives
  ↓
Plan
  ↓
Proof / Governor
  ↓
Execute
  ↓
Evidence
  ↓
Knowledge
  ↺
```

The key extension is that adaptation is governed by an explicit semantic Intent and constrained by invariants, policy, proof/evidence and capability boundaries.

Conceptually:

```text
Effect(r)
iff
Valid(r, Intent)
AND Conforms(r)
AND Authorized(r)
```

An LLM can contribute to proposing `r`, but cannot satisfy authorization by itself.

---

## 4. AllasCode extension: Event-Sourced Knowledge

MAPE-K defines shared Knowledge but does not prescribe Event Sourcing.

AllasCode adds Event Sourcing to provide:

- replayable historical truth;
- exact execution chronology;
- state reconstruction;
- idempotency context;
- last-known-good boundaries;
- causal/temporal evidence;
- support for compensation/restore/replay strategies.

This supports a feedback loop with durable history rather than only current-state adaptation.

---

## 5. AllasCode extension: Negative Knowledge

Classical feedback-loop descriptions emphasize observations, alternatives and adaptation decisions. AllasCode makes failed alternatives a first-class knowledge artifact.

```text
Positive Knowledge  = what is supported to work
Negative Knowledge  = what is supported not to work / is unsafe
Uncertain Knowledge = unresolved hypotheses
```

Negative Knowledge is used as a guardrail before expensive execution.

The guiding syntropic principle is:

> Every failure must reduce future uncertainty.

A failed repair should leave behind constraints, counterexamples, regression tests, causal evidence and provenance where possible.

---

## 6. AllasCode extension: Predictive self-healing

Reactive self-healing begins after a failure is observed.

Predictive self-healing attempts to detect a precursor trajectory before failure:

```text
current state
+ event sequence
+ metrics trend
+ trace structure
+ log observations
+ causal history
→ Risk(Failure)
```

When risk crosses a policy threshold, the system may produce a preventive plan, but prediction remains evidence rather than authority.

---

## 7. AllasCode extension: causal experimentation

Temporal order alone is insufficient for causation.

AllasCode therefore supports progressively stronger evidence:

```text
precedes
→ correlated_with
→ candidate_cause
→ intervention_supported
→ causal_supported
```

Controlled clone/sandbox experiments reconstruct pre-failure state and change candidate factors. This allows comparison of observational association with interventional behavior.

The design is intentionally conservative: causal confidence and counterexamples are preserved rather than silently promoting correlation into absolute cause.

---

## 8. AllasCode extension: Continuous Architectural Conformance

REMEDY compares Planned Architecture and Current Architecture. AllasCode extends this into continuous static + runtime conformance.

```text
Semantic Definition
├──→ Planned Architecture
└──→ Runtime/Generated Metadata
          ↓
    Current Architecture
          ↓
Static + Runtime Conformance
```

Architectural drift can itself become a healing event.

This is especially important for protecting boundaries such as:

```text
CodeManagerAgent must-not-mutate production
LLM must-not-actuate
Planner must-not-bypass ExecutionAgent
ExecutionAgent must-use sandbox before promotion
Actor must mediate AtomicAction execution
```

---

## 9. AllasCode extension: cognitive interpretation

The architecture exhibits functional capabilities analogous to a cognitive cycle without making a psychological claim about machine consciousness:

```text
Perception  → Monitor / observability
Memory      → EventStore / vector / graph / provenance
Learning    → positive + negative knowledge updates
Reasoning   → Analyzer / causal analysis
Planning    → CodeManagerAgent
Action      → governed ExecutionAgent
```

A scientifically safer description is **functional cognitive capabilities in an adaptive software runtime**, not a claim that the system possesses human cognition.

---

## 10. Relationship to semantic-temporal graph work

The causal topology generalizes the same structural idea used in semantic-temporal graph modeling: represent entities/states and their changing relations over time, then query not only similarity but transition structure.

For Cognitive Healing the modeled behavior is system behavior:

```text
SystemState(t0)
→ Event / Action / Observation
→ SystemState(t1)
→ new evidence
→ SystemState(t2)
```

The added layer is causal evidence, intervention and predictive risk.

---

## 11. Research claims that should be tested rather than assumed

The following are hypotheses to validate empirically:

1. Negative Knowledge reduces repeated failed repair attempts.
2. Causal-temporal retrieval improves repair selection over vector similarity alone.
3. Predictive precursor detection reduces realized failures without excessive false positives.
4. Sandbox interventions improve causal attribution over passive observability alone.
5. Event-Sourced replay improves reproducibility of healing experiments.
6. Continuous conformance prevents boundary erosion between planning and execution.
7. Semantic-first generation reduces token/code-generation cost while improving correctness through canonical reuse.
8. Syntropic failure-to-constraint conversion reduces uncertainty and regression rate over time.

These hypotheses should eventually be evaluated with explicit baselines, ablations and reproducible datasets.
