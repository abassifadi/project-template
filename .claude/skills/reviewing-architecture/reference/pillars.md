# Well-Architected review questions

## Contents
- Operational excellence
- Security
- Reliability
- Performance efficiency
- Cost optimization
- Sustainability

## Operational excellence
- Is all infrastructure defined as code and deployed through CI/CD?
- Are deployments small, frequent, automated, and reversible (blue/green, canary, feature flags)?
- Are there runbooks for common operations and incidents?
- Are SLOs defined, measured, and alerted on? Is there an on-call rotation?
- Are postmortems blameless and are action items tracked to completion?

## Security
- Is identity centralized with least privilege and no long-lived credentials?
- Are secrets stored in a secret manager and rotated?
- Is data encrypted in transit (TLS) and at rest? Who holds keys?
- Is the network segmented (private subnets, security groups, zero-trust service auth)?
- Is there a threat model? Are dependencies and images scanned?
- Are security events logged centrally, with detection and alerting?
- Is there an incident response plan that has been exercised?

## Reliability
- What are the SLOs, RPO, and RTO? Are they met by the design?
- Is every tier redundant across failure domains (AZs; regions if required)?
- Are backups automated, encrypted, and restore-tested on a schedule?
- Do clients use timeouts, retries with backoff + jitter, and circuit breakers?
- Are quotas/limits known and monitored? Does the system shed load gracefully?
- Has failure been tested (game days, chaos experiments)?

## Performance efficiency
- Are performance targets defined per critical flow?
- Were compute, storage, and database types chosen from measured workload characteristics?
- Is there caching where it measurably helps, with an invalidation strategy?
- Is load testing part of the release process for critical paths?
- Are data stores right for the access patterns (OLTP vs analytics vs search)?

## Cost optimization
- Is spend attributed per team/service with tags and reviewed regularly?
- Are resources right-sized; are idle resources removed automatically?
- Are pricing models used appropriately (commitments, spot/preemptible for tolerant workloads)?
- Are data transfer and storage lifecycle (tiering, retention) managed?
- Are budgets and anomaly alerts configured?

## Sustainability
- Is utilization high (right-sizing, autoscaling, scale-to-zero where possible)?
- Are regions/instance types chosen with efficiency in mind?
- Is data retention minimized, and are unnecessary data copies avoided?
- Are batch workloads scheduled flexibly?
