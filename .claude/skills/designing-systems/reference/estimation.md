# Capacity estimation cheat sheet

## Contents
- Time and traffic conversions
- Latency numbers
- Storage sizing
- Availability math
- Worked example

## Time and traffic conversions

- 1 day ≈ 86,400 s ≈ 10^5 s (use 10^5 for quick math)
- 1M requests/day ≈ 12 req/s average
- 100M requests/day ≈ 1,200 req/s average
- Peak = average × 2–10 depending on diurnal pattern; use × 3 if unknown, state the assumption.

## Latency numbers (orders of magnitude)

| Operation | Approx. |
|-----------|---------|
| L1 cache reference | ~1 ns |
| Main memory reference | ~100 ns |
| Read 1 MB sequentially from memory | ~10 µs |
| SSD random read | ~100 µs |
| Round trip within a datacenter / AZ | ~0.5 ms |
| Read 1 MB sequentially from SSD | ~1 ms |
| Round trip across regions (same continent) | ~10–40 ms |
| Round trip intercontinental | ~100–150 ms |

## Storage sizing

`storage = records/day × bytes/record × retention days × replication factor × (1 + index/overhead ≈ 0.3–1.0)`

Typical sizes: UUID 16 B, timestamp 8 B, small JSON event 0.5–2 KB, image thumbnail 20–50 KB, photo 2–5 MB.

## Availability math

| SLO | Downtime / month | Downtime / year |
|-----|------------------|-----------------|
| 99% | 7.3 h | 3.65 d |
| 99.9% | 43.8 min | 8.77 h |
| 99.95% | 21.9 min | 4.38 h |
| 99.99% | 4.38 min | 52.6 min |

- Serial dependencies multiply: A (99.9%) → B (99.9%) ≈ 99.8%.
- Redundant replicas: 1 − (1 − a)^n, assuming independent failures (they rarely are; account for shared fate).

## Worked example: URL shortener

- 100M new URLs/month → ~40 writes/s avg, ~120 peak.
- Read:write 100:1 → ~4,000 reads/s avg, ~12,000 peak → cache hot keys.
- 500 B per record × 100M × 12 months × 5 years = 3 TB raw; × 3 replicas ≈ 9 TB.
- 7-char base62 key space = 62^7 ≈ 3.5 × 10^12 → ample for 6 × 10^9 URLs.
