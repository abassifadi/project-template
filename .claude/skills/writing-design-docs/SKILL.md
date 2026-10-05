---
name: writing-design-docs
description: Writes technical design documents and RFCs — problem statement, goals and non-goals, proposed design, alternatives, risks, rollout, and open questions — for review before implementation. Use when planning a significant feature, project, or cross-team change, or when the user asks for a design doc, RFC, tech spec, or one-pager.
argument-hint: "[feature or project name]"
metadata:
  role: system-architect
  version: "1.0"
---

# Writing design docs

A design doc exists to get the design reviewed *before* code is expensive to change. Optimize for the reviewer's time.

## Workflow

1. **Gather context.** Read the related code, ADRs, and issues. Ask the user for the problem, stakeholders, deadlines, and constraints you cannot find. Do not invent requirements.
2. **Write goals and non-goals first** and confirm them with the user. Most bad designs come from unclear scope.
3. **Draft the design** using the template. Lead with the decision; put detail later.
4. **Show alternatives honestly**, including "do nothing".
5. **Self-review** with the checklist below, then hand off for review.
6. **After review**, extract each significant decision into an ADR (`writing-adrs`).

## Template

```markdown
# <Title>

| Author | Reviewers | Status | Last updated |
|--------|-----------|--------|--------------|
| | | Draft / In review / Approved / Superseded | YYYY-MM-DD |

## TL;DR
<3–5 sentences: problem, proposed solution, key trade-off, ask of the reader.>

## Context and problem
<Why now? What is broken or missing? Data that shows the problem.>

## Goals
- <measurable outcome>
## Non-goals
- <explicitly out of scope, to prevent scope creep>

## Proposed design
### Overview
<diagram (C4 container level) + one-paragraph walkthrough of the main flow>
### Detailed design
<components, APIs/contracts, data model and migrations, algorithms>
### Cross-cutting concerns
- Security & privacy: <threat model summary>
- Reliability: <failure modes, SLO impact>
- Performance & capacity: <estimates>
- Observability: <metrics, alerts>
- Cost: <estimate>

## Alternatives considered
### <Alternative A>
<summary, pros, cons, why not chosen>

## Rollout plan
<phases, feature flags, migration steps, backward compatibility, rollback>

## Testing strategy
## Risks and mitigations
## Open questions
## Milestones
```

## Self-review checklist

- [ ] A reader can state the problem and proposal after reading only the TL;DR
- [ ] Goals are measurable; non-goals are present
- [ ] At least one real alternative with fair trade-offs
- [ ] Failure modes, security, and rollback addressed
- [ ] Every open question has an owner
- [ ] Under ~6 pages; detail moved to appendices
