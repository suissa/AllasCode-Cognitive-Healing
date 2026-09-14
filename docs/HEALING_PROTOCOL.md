# Healing Protocol

## 1. End-to-end protocol

```text
Observation
→ Diagnosis
→ Strategy Filtering
→ RepairProposal
→ Sandbox Execution
→ Validation
→ Evidence
→ Governor Decision
→ Promotion / Recovery
→ Knowledge Update
```

## 2. Observation

The runtime emits an `Observation` whenever a relevant event, state transition, anomaly or scheduled monitoring boundary occurs.

The Monitor does not diagnose.

## 3. Diagnosis

The Analyzer correlates:

- Event Sourcing history;
- current state;
- semantic/vector retrieval;
- causal-temporal topology;
- logs;
- traces;
- metrics;
- failed repairs;
- successful repairs;
- invariants and policies.

The Analyzer may classify a condition as:

```text
healthy
anomaly
active_failure
predicted_failure
architectural_drift
unknown
```

Unknown/unresolved conditions can ultimately escalate to Human-in-the-Healing-Loop.

## 4. Candidate strategy space

A diagnosis can yield multiple healing alternatives:

```text
retry
reconnect
reconfigure
switch dependency/replica
repair code
compensate
restore
replay
preventive mitigation
human escalation
```

The Planner SHOULD score candidates using expected benefit and risk rather than fixed retry order.

Conceptual scoring:

```text
Score(r) =
  ExpectedSuccess(r)
- FailureRisk(r)
- KnownBadSimilarity(r)
- Cost(r)
- Latency(r)
- InvariantRisk(r)
```

Only candidates satisfying hard safety/invariant constraints are rankable.

## 5. Negative-Knowledge gate

Before any execution, CodeManagerAgent evaluates the candidate against known negative knowledge.

```text
candidate
→ semantic similarity to failed repairs
→ causal-context equivalence
→ invariant/policy checks
→ known side-effects
→ generated regression constraints
```

Outcomes:

```text
known unsafe      → reject
known invalid     → reject unless new evidence justifies re-evaluation
context mismatch  → continue with caution
uncertain         → sandbox experiment
supported         → proceed to execution authorization
```

This gate can reject an LLM-generated proposal without spending resources on build/test execution.

## 6. LLM mediation

The CodeManagerAgent SHOULD avoid asking an LLM to rediscover known solutions.

If an LLM is required, the Planner provides a bounded prompt with:

- current diagnosis;
- affected semantic scope;
- relevant causal chain;
- known-good historical strategies;
- known-bad historical strategies;
- forbidden strategies;
- invariants;
- expected behavior;
- validation criteria.

The returned output is a **proposal**, never authority.

## 7. RepairProposal

```text
RepairProposal
├── proposal_id
├── failure_episode_id
├── diagnosis_id
├── hypothesis
├── intended_effect
├── files/components in scope
├── semantic changes
├── patch/change set
├── expected state
├── expected observability deltas
├── required tests[]
├── required causal experiments[]
├── rollback/recovery plan
├── invariants[]
├── provenance refs[]
└── confidence
```

## 8. Git attempt protocol

Every code mutation in a healing workspace MUST be committed before validation.

Example:

```text
commit A: repair attempt
run validation
if failed:
  commit B: failure evidence (may be empty)
  update Healing Knowledge
  generate regression constraint when reproducible
  create next attempt
```

Suggested trailers:

```text
Repair-ID: hr_0182
Failure-ID: fe_0091
Hypothesis: stale pooled connection
Validation: failed
Error-Signature: vector.timeout.v3
```

Failed attempts are preserved as learning/provenance artifacts.

## 9. Validation ladder

Depending on the proposal, validation may include:

1. schema/semantic validation;
2. compilation/build;
3. unit tests;
4. integration tests;
5. property tests;
6. invariant tests;
7. regression tests generated from prior failures;
8. architectural conformance checks;
9. chaos/fault-injection tests;
10. load/performance tests;
11. observability comparison;
12. causal experiment validation;
13. staging/canary evidence.

Tests SHOULD validate behavior rather than overfit implementation details.

## 10. Failed repair becomes executable knowledge

When deterministic reproduction is possible:

```text
FailedRepair
→ extract invalid condition
→ generate regression constraint
→ commit test
→ attach test to failure episode
→ require future candidates to pass it
```

A regression constraint may be implemented as:

- unit test;
- integration test;
- property test;
- invariant test;
- chaos test;
- load test;
- architectural conformance test.

## 11. Promotion

`Verified in Sandbox` is not equivalent to `Approved for Promotion`.

Promotion requires Governor authorization and may proceed through clone/staging/canary boundaries before production.

## 12. Knowledge update

Every completed attempt updates relevant Knowledge classes:

```text
Runtime Knowledge      ← new events/evidence
Observability Knowledge← validation telemetry
Healing Knowledge      ← success/failure episode
Causal Knowledge       ← supported/refuted relations
Provenance Knowledge   ← commits/tests/lineage
```

This closes the adaptive loop.
