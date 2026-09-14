# Spec-Driven Integration

## 1. Objective

The long-term goal is to move AI away from arbitrary code generation and toward **semantic declaration generation**.

The developer models the problem, business rules, entities, intentions, constraints and desired behavior. AllasCode resolves those semantics into architecture and canonical implementation artifacts.

```text
Human / Product Spec
      ↓
Spec-Driven Development
      ↓
Semantic Normalization
      ↓
AllasCode IR
      ↓
Canonical Resolution
      ↓
Architecture Compilation
      ↓
Runtime Artifacts
```

## 2. Why an intermediate representation is mandatory

AllasCode MUST NOT depend on one specific SDD tool.

Different sources should compile into a common semantic intermediate representation:

```text
Spec Kit ──────────────┐
OpenSpec ──────────────┤
Chat interview ────────┤
Markdown requirements ─┤
JSON/YAML spec ─────────┤──→ AllasCode IR
External SDD tools ─────┘
```

The IR is the stable contract between problem specification and architecture generation.

## 3. AllasCode IR minimum concepts

```text
AllasCodeIR
├── Contexts
├── Entities
├── Properties
├── Relations
├── Intents
├── Behaviors
├── Flows
├── Events
├── Invariants
├── Constraints
├── Policies
├── Capabilities
├── DataRequirements
├── ObservabilityRequirements
├── AcceptanceCriteria
└── ProofObligations
```

The AI may help infer these elements from natural language, but the resulting IR is schema-validated and deterministic before architecture resolution.

## 4. Semantic-first generation

Preferred model:

```text
Prompt
→ LLM
→ Semantic Declaration
→ Schema Validation
→ Intent Resolution
→ Canonical Components
→ Invariants / Proofs
→ Deterministic Generation
```

Avoid the default model:

```text
Prompt
→ LLM
→ arbitrary implementation code
→ tests written around the generated code
→ uncertain correctness
```

## 5. Canonical behavior registry

The universal registry is not merely a code package registry. It stores semantic behavior plus evidence.

```text
CanonicalBehavior
├── canonical_label
├── semantic_contract
├── compatible_intents[]
├── implementation_version
├── implementation_refs
├── invariants[]
├── tests[]
├── proof_obligations[]
├── conformance evidence
├── security evidence
├── benchmark evidence
├── operational evidence
├── known failure episodes[]
├── negative knowledge[]
├── supported environments[]
└── supersession lineage
```

## 6. Meaning of canonical

`Canonical` MUST NOT mean permanently optimal.

It means:

> the best currently accepted implementation under explicit constraints and available evidence.

A challenger may replace it:

```text
Canonical v3
→ Challenger v4
→ benchmark
→ proof
→ regression suite
→ security validation
→ sandbox/canary
→ evidence review
→ v4 becomes canonical
```

## 7. Canonical resolution

When a semantic declaration requests behavior:

```text
Intent / Behavior
→ canonical_label + aliases
→ semantic/vector lookup
→ candidate canonical behaviors
→ compatibility/invariant check
→ select implementation
```

If a compatible canonical behavior exists, arbitrary code generation SHOULD NOT be the first option.

## 8. Unknown behavior path

If no canonical behavior satisfies the request:

```text
Unknown Intent/Behavior
→ CodeManagerAgent
→ research / candidate strategies
→ semantic design
→ optional LLM proposal
→ isolated implementation
→ tests / proofs / benchmarks
→ healing knowledge integration
→ review for canonical promotion
```

The new implementation enters the universal registry only after sufficient evidence.

## 9. Relationship to Cognitive Healing

Cognitive Healing protects this pipeline in two ways:

1. **Before generation** — Negative Knowledge, canonical reuse and constraints reduce the search space.
2. **After generation** — sandbox execution, tests, causal experiments, conformance and provenance prevent unverified AI output from becoming a system effect.

## 10. Developer role

The architecture shifts the developer toward problem and business modeling:

```text
Developer focuses on:
- problem
- context
- business model
- domain semantics
- constraints
- trade-offs
- creativity

AllasCode focuses on:
- formalization
- architecture resolution
- canonical reuse
- verification
- implementation generation
- execution
- observation
- healing
```

This is not based on the claim that programming languages disappear. It is an architectural goal to make implementation-language and infrastructure concerns less dominant in the developer's primary workflow.

## 11. Cognitive support for the developer

The development environment may also deliberately support three complementary problem-solving modes:

```text
Analytical
→ constraints, evidence, costs, future maintenance, trade-offs

Creative
→ alternatives, analogies, new compositions, solution exploration

Practical
→ concrete semantic specification and immediate executable path
```

The system should not force one cognitive mode. It should surface missing perspectives so the developer can spend more attention on solution design than boilerplate implementation.
