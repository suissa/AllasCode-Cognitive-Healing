# Causal-Temporal Topology

## 1. Goal

The causal-temporal topology is the reasoning substrate for predictive self-healing. It extends a temporal semantic graph with runtime, healing and observability semantics so the Analyzer can reason about trajectories, precursors, interventions and outcomes.

The graph must distinguish **temporal precedence**, **correlation**, **risk contribution** and **causal support**. `A happened before B` is never sufficient to assert `A caused B`.

## 2. Node model

```text
CausalTopology
├── Semantic Nodes
│   ├── Intent
│   ├── Agent
│   ├── Actor
│   ├── Action
│   ├── Event
│   ├── Error
│   ├── Invariant
│   ├── Healing
│   └── Hypothesis
├── Runtime Nodes
│   ├── State
│   ├── Dependency
│   └── Resource
├── Observability Nodes
│   ├── MetricSnapshot
│   ├── Trace
│   └── LogObservation
└── Experimental Nodes
    ├── CausalExperiment
    ├── Intervention
    └── Outcome
```

## 3. Edge model

Minimum relation vocabulary:

```text
precedes
follows
contains
observed_during
correlated_with
candidate_cause
increases_risk_of
causes
mitigates
resolves
prevents
triggers
evidences
attempted_by
failed_with
recovered_by
intervention_supported
```

The `causes` relation SHOULD be reserved for cases with explicit evidence policy. In most runtime scenarios, `causal_supported` or `increases_risk_of` is safer than an absolute causal assertion.

## 4. Edge evidence

Each non-trivial causal edge SHOULD carry evidence metadata:

```text
CausalEvidence
├── support_count
├── counterexample_count
├── confidence
├── effect_size
├── first_seen
├── last_seen
├── time_window
├── source_episode_ids[]
├── experiment_ids[]
├── environment_hashes[]
└── evidence_level
```

Suggested evidence levels:

```text
observed
correlated
candidate_cause
intervention_supported
causal_supported
```

## 5. Observability as graph evidence

### MetricSnapshot

Represents quantitative state at a moment or narrow time window:

```text
MetricSnapshot
├── timestamp
├── cpu
├── memory
├── latency_p50
├── latency_p95
├── latency_p99
├── error_rate
├── retry_rate
├── queue_lag
├── connection_pool
└── custom_metrics
```

Trends are often more predictive than individual values:

```text
memory: 61 → 72 → 84 → 93
latency: 80ms → 150ms → 420ms → 1.8s
retry:   0.2% → 1% → 4% → 12%
```

A recurring trajectory can become a precursor pattern.

### Trace

A Trace captures the execution path and can connect Intent, Actor, Actions, dependencies, failure and recovery.

```text
Trace
├── trace_id
├── root_span
├── spans[]
├── start_time
├── end_time
├── duration
├── status
└── causal_path
```

### LogObservation

The graph SHOULD store a semantic observation/reference, not every raw log line.

```text
LogObservation
├── timestamp
├── source
├── severity
├── semantic_label
├── structured_fields
├── trace_id
├── action_id
└── entity_id
```

Raw logs, traces and metrics remain in specialized stores; graph nodes point to them.

## 6. Predictive risk

The Analyzer estimates risk using the current graph and observation context:

```text
Risk(Failure_i | G_t, O_t) > threshold_i
    => emit PreventiveDiagnosisCandidate
```

Where:

- `G_t` is the current causal-temporal topology;
- `O_t` is the current observation window;
- `Failure_i` is a candidate future failure.

A high risk score is not authorization to act.

## 7. Precursor learning

Example:

```text
State A
  ↓
Event B
  ↓
Latency ↑
  ↓
Retry ↑
  ↓
Dependency C degraded
  ↓
Error X
```

Repeated episodes can promote a sequence from an observed temporal pattern to a risk relation:

```text
Pattern(A,B,C) --increases_risk_of--> Error X
```

## 8. Causal experimentation

To test whether a predecessor is a cause rather than a coincidental precursor, the system can reproduce the pre-failure state in an isolated clone and perform controlled interventions.

```text
Production episode
→ restore/replay pre-failure state
→ generate hypothesis
→ control relevant variables
→ apply intervention/fault injection
→ run repeated trials
→ compare treatment/control outcomes
→ update edge evidence
```

The core distinction is observational versus interventional reasoning:

```text
P(Error X | A)       = observational association
P(Error X | do(A))   = interventional evidence
```

A conceptual experiment record:

```text
CausalExperiment
├── experiment_id
├── source_episode
├── replay_environment_hash
├── hypothesis
├── intervention
├── controlled_variables[]
├── changed_variables[]
├── treatment_trials
├── control_trials
├── treatment_failure_rate
├── control_failure_rate
├── effect_size
├── confidence
├── counterexamples[]
└── outcome
```

## 9. Counterfactual replay

Event Sourcing makes a practical approximation of counterfactual testing possible:

```text
replay to t-1
→ reproduce state
→ remove/change candidate precursor A
→ execute forward
→ compare outcome
```

This does not make hidden confounding disappear, so the system should record the strength and scope of causal evidence rather than overclaim certainty.

## 10. Reuse of semantic-temporal graph concepts

The topology is compatible with prior AllasCode ecosystem work on semantic temporal graphs: time-bounded nodes/edges, semantic search and temporal transitions can be generalized from human conversational/behavioral transformations to system behavioral transformations.

The domain changes, but the central question remains the same:

```text
What state existed?
What changed?
What preceded the transition?
What evidence explains why the next state emerged?
```
