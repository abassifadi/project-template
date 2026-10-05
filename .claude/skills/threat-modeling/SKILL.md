---
name: threat-modeling
description: Performs threat modeling of a system or feature using data flow diagrams and STRIDE, following the Threat Modeling Manifesto's four questions, and produces a prioritized threat register with mitigations. Use when designing features that touch authentication, payments, PII, multi-tenancy, or external integrations, or when the user asks about attack surface or "what could go wrong" security-wise.
metadata:
  role: system-architect
  version: "1.0"
---

# Threat modeling

Answer the four questions of the Threat Modeling Manifesto:
1. What are we working on? 2. What can go wrong? 3. What are we going to do about it? 4. Did we do a good enough job?

## Workflow

**1. Model the system.** Draw a data flow diagram (Mermaid `flowchart` is fine) with:
- External entities (users, third parties)
- Processes (services, functions)
- Data stores
- Data flows (label with data type and protocol)
- **Trust boundaries** (internet ↔ DMZ, service ↔ DB, tenant ↔ tenant, user ↔ admin)

List assets worth protecting (credentials, PII, payment data, business secrets, availability) and who the plausible attackers are.

**2. Find threats with STRIDE** — walk every element and every flow that crosses a trust boundary:

| Threat | Violates | Ask | Typical mitigations |
|--------|----------|-----|---------------------|
| **S**poofing | Authentication | Can someone pretend to be another user/service? | MFA, mTLS, signed tokens, strong session mgmt |
| **T**ampering | Integrity | Can data be modified in transit or at rest? | TLS, signatures/HMAC, input validation, immutable logs |
| **R**epudiation | Non-repudiation | Can someone deny an action? | Audit logs with identity + timestamp, tamper-evident storage |
| **I**nformation disclosure | Confidentiality | Can data leak (errors, logs, IDOR, side channels)? | Authz per object, encryption, data minimization, redaction |
| **D**enial of service | Availability | Can someone exhaust resources? | Rate limits, quotas, timeouts, autoscaling, input size limits |
| **E**levation of privilege | Authorization | Can someone gain rights they should not have? | Least privilege, deny-by-default, sandboxing, server-side checks |

Also consider abuse of legitimate features (scraping, fraud, enumeration) and supply chain (dependencies, CI/CD, base images).

**3. Respond.** For each threat choose: mitigate, eliminate (remove the feature/data), transfer (third party, insurance), or accept (with an owner who signs off). Rate risk = likelihood × impact (High/Medium/Low), or use CVSS / OWASP Risk Rating if the team already does.

**4. Validate.** Every High threat has a mitigation with a ticket and a verification method (test, pen-test item, config check). Revisit the model when the design changes.

## Threat register format

```markdown
| ID | Element / flow | STRIDE | Threat | Likelihood | Impact | Risk | Response | Mitigation | Owner | Status |
|----|----------------|--------|--------|------------|--------|------|----------|------------|-------|--------|
| T1 | Browser → API | S | Stolen session token replayed | M | H | High | Mitigate | Short-lived tokens, rotation, bind to device | @team | Open |
```

Store the model at `docs/security/threat-model-<feature>.md` and link it from the related ADR or design doc.
