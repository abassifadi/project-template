---
name: designing-systems
description: Designs software systems end to end — requirements, quality attributes, capacity estimates, component and data design, trade-off analysis, and failure modes — producing an architecture proposal. Use when designing a new system or major feature, choosing between architectures (monolith, microservices, event-driven, serverless), or answering "how should we build X".
metadata:
  role: system-architect
  version: "1.0"
---

# Designing systems

Architecture is the set of decisions that are expensive to change. Make them explicitly, with trade-offs written down.

## Workflow

```
Design progress:
- [ ] 1. Clarify functional requirements
- [ ] 2. Quantify quality attributes (NFRs)
- [ ] 3. Estimate capacity
- [ ] 4. Identify constraints and context
- [ ] 5. Propose 2–3 candidate architectures
- [ ] 6. Evaluate trade-offs and choose
- [ ] 7. Detail components, data, and interfaces
- [ ] 8. Analyze failure modes
- [ ] 9. Record decisions (ADRs) and diagrams (C4)
```

**1. Functional requirements.** Actors, top use cases, explicitly out-of-scope items. Ask clarifying questions rather than assuming.

**2. Quality attributes** (ISO/IEC 25010). Make each measurable — a quality attribute scenario:
`<source> <stimulus> <environment> → <response> <measure>`, e.g. "1,000 users submit orders at peak → 99% complete in < 500 ms".
Cover: performance, scalability, availability (SLO, e.g. 99.9%), durability (RPO), recovery (RTO), security, privacy/compliance, maintainability, cost.

**3. Capacity.** Back-of-envelope: DAU → requests/s (avg and peak ≈ 2–10× avg), read:write ratio, payload size → bandwidth, records/day × retention → storage. Show the arithmetic. See [reference/estimation.md](reference/estimation.md).

**4. Constraints.** Team size and skills, existing platforms, budget, deadlines, regulations (GDPR, HIPAA, PCI DSS), build-vs-buy.

**5. Candidates.** Sketch 2–3 genuinely different options. Default toward the simplest that meets the NFRs: a well-structured modular monolith beats premature microservices for small teams. See [reference/patterns.md](reference/patterns.md).

**6. Trade-offs.** Score options against the NFRs from step 2:

| Attribute (weight) | Option A | Option B | Option C |
|--------------------|----------|----------|----------|
| Latency p99 (3) | | | |
| Ops complexity (2) | | | |
| Cost (2) | | | |
| Team fit (1) | | | |

State what you are giving up, not only what you gain.

**7. Detail.** Components and responsibilities, sync vs async interactions, API contracts (`designing-apis`), data stores and ownership (`designing-data-models`), consistency model per data flow.

**8. Failure modes.** For each dependency: what if it is slow, down, or returns garbage? Apply timeouts, retries with backoff + jitter, circuit breakers, bulkheads, idempotency, graceful degradation. Identify single points of failure and the blast radius.

**9. Record.** One ADR per significant decision (`writing-adrs`); C4 context + container diagrams (`modeling-c4-architecture`); threat model (`threat-modeling`); observability plan (`planning-observability`).

## Output: architecture proposal

```markdown
# <System> architecture proposal
## Context and goals
## Requirements (functional, quality attribute scenarios)
## Capacity estimates
## Options considered (with trade-off table)
## Chosen architecture (C4 diagrams, components, data, interfaces)
## Failure modes and mitigations
## Security and compliance
## Rollout and migration plan
## Open questions and risks
```
