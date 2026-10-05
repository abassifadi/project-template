---
name: optimizing-performance
description: Diagnoses and fixes latency, throughput, memory, and cost problems by measuring first — profiling, benchmarking, query analysis, caching, and load testing. Use when something is slow, uses too much CPU/memory, times out, costs too much, or the user asks to make code faster or scale.
metadata:
  role: senior-developer
  version: "1.0"
---

# Optimizing performance

Measure, don't guess. Most time is spent in a few places, and they are rarely where intuition says.

## Workflow

1. **Define the target.** A number and a percentile: "p99 checkout latency < 300 ms at 200 RPS", "batch job < 10 min", "RSS < 512 MB". No target → no optimization.
2. **Baseline.** Reproduce the slow path with realistic data volume. Record the current numbers and the exact command/environment.
3. **Profile** to find where time/memory actually goes:
   - CPU: `py-spy`, `pprof` (Go), `async-profiler` (JVM), Chrome/Node `--cpu-prof`, `perf` + flame graphs.
   - Memory: heap snapshots, `tracemalloc`, `pprof -alloc_space`.
   - Database: `EXPLAIN (ANALYZE, BUFFERS)`, slow query log, `pg_stat_statements`.
   - Distributed: traces (OpenTelemetry) to see which span dominates.
4. **Fix the biggest item only**, then re-measure. Repeat until the target is met. Stop when it is.
5. **Guard the win** with a benchmark or load test in CI, or an SLO alert.

## Usual suspects (check in this order)

1. **N+1 queries / chatty I/O** → batch, join, `IN (...)`, dataloader.
2. **Missing or wrong index** → index matching `WHERE` + `ORDER BY`; check the plan uses it.
3. **Unbounded work** → pagination, limits, streaming instead of loading all into memory.
4. **Serial I/O that could be concurrent** → parallelize independent calls with bounded concurrency.
5. **Wrong algorithm / data structure** → O(n²) loops, list lookups that should be sets/maps.
6. **Repeated expensive work** → memoize or cache (see below).
7. **Serialization / allocation churn** → reuse buffers, avoid needless copies.
8. **Infrastructure** → connection pool size, timeouts, instance size — only after code-level causes.

## Caching rules

- Cache only after measuring; define key, TTL, invalidation, and size bound up front.
- Prefer caching immutable or versioned data.
- Protect against stampede (request coalescing, jittered TTL).
- A cache must never be the only copy of data; the system must work (slowly) when it is empty.

## Load testing

Use k6, Gatling, Locust, or JMeter with a realistic traffic mix and ramp. Watch latency percentiles (p50/p95/p99), error rate, and saturation (CPU, memory, connections, queue depth). Find the knee where latency climbs — that is capacity.

## Report format

```markdown
**Target:** <metric @ load>
**Baseline:** <numbers + how measured>
**Bottleneck:** <profile evidence>
**Change:** <what and why>
**Result:** <new numbers, same method>  (<x>% improvement)
**Trade-offs:** <memory, complexity, staleness, cost>
```
