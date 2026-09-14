# Knowledge Model

## 1. Principle

`Knowledge` is not equivalent to LLM memory.

The Knowledge plane is the authoritative and evidentiary substrate used by Monitor, Analyzer, Planner and Execute. The LLM receives only a context projection derived from it.

```text
Knowledge != LLM Memory
```

## 2. Knowledge classes

```text
Knowledge
├── Runtime Knowledge
├── Observability Knowledge
├── Healing Knowledge
├── Causal Knowledge
└── Provenance Knowledge
```

### Runtime Knowledge

Contains execution truth:

- EventStore;
- current consolidated state;
- snapshots;
- event offsets;
- execution evidence;
- Actor/Action state;
- scheduler state;
- idempotency information.

Event Sourcing is the historical truth layer. It allows replay, exact reconstruction and comparison between pre-failure and post-repair states.

### Observability Knowledge

Contains operational-system evidence that may not exist in entity/domain state:

- logs;
- traces;
- metrics;
- profiles;
- health checks;
- queue/stream lag;
- CPU/memory/network/storage telemetry;
- scheduler signals;
- SLO/SLA state.

Raw telemetry SHOULD remain in specialized stores. The causal graph stores semantically relevant observations and references rather than duplicating all raw telemetry.

### Healing Knowledge

```text
HealingKnowledge
├── FailureEpisodes
├── SuccessfulRepairs
├── FailedRepairs
├── RejectedRepairs
├── UnsafeRepairs
├── ContextSpecificFailures
└── RegressionConstraints
```

A `FailureEpisode` SHOULD preserve:

```text
FailureEpisode
├── failure_episode_id
├── canonical_label
├── agent_id
├── actor_id
├── action_id
├── intent_id
├── input_shape
├── state_before
├── error_type
├── error_message
├── trace/log/metric refs
├── violated_invariants[]
├── suspected_root_cause
├── attempted_repairs[]
├── selected_repair
├── patch_hash
├── tests_before
├── tests_after
├── proof_results
├── state_after
├── success
└── evidence
```

### Causal Knowledge

Stores semantic-temporal relationships and evidence strength between runtime phenomena.

See `CAUSAL_TOPOLOGY.md`.

### Provenance Knowledge

Stores immutable lineage of repair work:

- original commit;
- repair-attempt commits;
- validation-result commits;
- final repair commit;
- generated tests;
- patch hashes;
- evidence references;
- causal experiment IDs.

## 3. Positive, Negative and Uncertain Knowledge

Healing Knowledge is classified epistemically:

```text
Knowledge
├── PositiveKnowledge
│   └── strategies supported by successful evidence
├── NegativeKnowledge
│   └── strategies supported as invalid/unsafe in a causal context
└── UncertainKnowledge
    └── hypotheses with insufficient evidence
```

Negative Knowledge is first-class.

A failed repair is not discarded. It constrains future candidate generation and can become an executable regression constraint.

## 4. Contextual validity

A repair is not globally labeled `bad` solely because it failed once.

Every conclusion SHOULD retain context such as:

- dependency versions;
- runtime/compiler versions;
- hardware/resource conditions;
- policy versions;
- state hash;
- causal neighborhood;
- temporal interval;
- confidence/evidence count.

This supports re-evaluation when new evidence or environmental changes invalidate an old conclusion.

## 5. Semantic/vector retrieval

Vector retrieval answers:

```text
"What prior episodes are semantically similar to this one?"
```

Candidate embeddings may be produced for:

- current failure;
- repair attempt;
- patch diff;
- reasoning summary;
- failure context;
- validation result.

This enables pre-execution rejection of repair candidates that are semantically close to previously proven-bad solutions in an equivalent context.

## 6. Causal graph retrieval

The graph answers different questions:

```text
What preceded this failure?
What tends to happen next?
Which Action usually fails before this Error?
Which repair mitigated this causal pattern?
Which repair caused a secondary failure?
Which precursor increases the risk of this failure?
```

The vector store supplies semantic similarity; the graph supplies structure, temporal relations and causal evidence; Event Sourcing supplies historical truth.

## 7. Syntropic knowledge growth

AllasCode adopts a syntropic requirement:

> Every failure must reduce future uncertainty.

A failed execution MUST add at least one useful knowledge artifact when evidence permits:

- constraint;
- counterexample;
- negative-repair episode;
- causal update;
- regression test;
- provenance evidence;
- revised confidence score.

Conceptually:

```text
K(t+1) contains more decision-relevant evidence than K(t)
after a completed learning cycle.
```

This is not a claim that all stored information monotonically increases forever; obsolete conclusions may be superseded. The invariant concerns retained decision-relevant evidence and traceability.

## 8. Paper grounding

REMEDY models a shared MAPE-K `Knowledge` abstraction and enriches it with lower-level concepts including `ReferenceInput` and `Alternative`. The paper explains `ReferenceInput` as the concrete values describing the state to be achieved and `Alternative` as available adaptation options. AllasCode maps these ideas to expected Intent/invariant state and candidate healing alternatives, while extending Knowledge with Event Sourcing, causal-temporal evidence, observability and provenance.

Reference: San Martín, Camargo, Angulo, _Architectural Conformance Checking for MAPE-K-based Self-Adaptive Systems_, arXiv:2401.16382v3, 2025.
