# Architecture patterns: when to use what

## Contents
- Architecture styles
- Integration and data patterns
- Resilience patterns
- Consistency choices

## Architecture styles

| Style | Choose when | Avoid when |
|-------|-------------|------------|
| Modular monolith | One team or a few; domain still evolving; need fast iteration | Parts need independent scaling or release cadence by separate teams |
| Microservices | Many autonomous teams; clear bounded contexts; independent deploy/scale needed | Small team; unclear boundaries; no platform/observability maturity |
| Event-driven | Many consumers react to the same facts; temporal decoupling; audit trail | Need immediate consistency and simple request/response debugging |
| Serverless (FaaS) | Spiky or low traffic; glue/event processing; minimal ops | Long-running, latency-critical, or steady high-throughput workloads (cost) |
| Hexagonal / ports & adapters | Keep domain logic independent of frameworks and I/O | Trivial CRUD with no domain logic |
| CQRS | Read and write models differ greatly in shape or scale | Simple CRUD — adds complexity |

Conway's Law: system boundaries will mirror team boundaries. Draw service boundaries along Domain-Driven Design bounded contexts and team ownership.

## Integration and data patterns

- **Database per service**: each service owns its data; others go through its API or events. Never share tables across services.
- **Transactional outbox**: write the event to an outbox table in the same DB transaction, relay it to the broker. Avoids dual-write inconsistency.
- **Saga** (choreography or orchestration): multi-service business transactions with compensating actions instead of distributed transactions.
- **API gateway / BFF**: single entry for clients; auth, rate limiting, aggregation per client type.
- **Strangler fig**: incrementally replace a legacy system behind a routing façade.
- **Anti-corruption layer**: translate a legacy or external model so it does not leak into your domain.

## Resilience patterns

- **Timeouts** on every remote call (shorter than the caller's timeout).
- **Retries** only for idempotent operations, with exponential backoff + jitter and a retry budget.
- **Circuit breaker** to fail fast when a dependency is unhealthy.
- **Bulkhead**: isolate resource pools so one dependency cannot exhaust all threads/connections.
- **Rate limiting / load shedding**: protect yourself under overload; return 429/503 early.
- **Graceful degradation**: serve cached or partial results when non-critical dependencies fail.
- **Idempotency keys** so retries and duplicate messages are safe.

## Consistency choices

- CAP: under a network partition you choose consistency or availability. PACELC adds: else, latency vs consistency.
- Use strong consistency for money, inventory reservation, uniqueness constraints.
- Use eventual consistency for feeds, search indexes, analytics, notifications — and design the UI for it.
- Message delivery is at-least-once in practice: make consumers idempotent.
