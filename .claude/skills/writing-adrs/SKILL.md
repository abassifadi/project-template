---
name: writing-adrs
description: Writes and maintains Architecture Decision Records (ADRs) in MADR format, capturing context, options, decision, and consequences, and keeps the ADR log numbered and linked. Use when an architectural or technology decision is made or proposed, when choosing between libraries, databases, or patterns, or when the user asks to document a decision.
argument-hint: "[decision title]"
metadata:
  role: system-architect
  version: "1.0"
---

# Writing ADRs

An ADR records one significant decision so future readers know *why*, not only *what*. Write it when the decision is made, not months later.

## When to write one

Write an ADR for decisions that are hard to reverse or affect many people: architecture style, database/broker choice, framework, API style, auth approach, hosting, major dependency, cross-cutting conventions. Do not write one for routine implementation choices.

## Workflow

1. **Find the log.** Look for `docs/adr/`, `docs/decisions/`, or `adr/`. If none exists, create `docs/adr/` and add `0000-record-architecture-decisions.md` using the template.
2. **Number it.** Next sequential four-digit number; filename `NNNN-kebab-case-title.md`. Title is the decision in imperative form: "Use PostgreSQL for order storage".
3. **Fill the template** below with the user. Ask for missing context — do not invent drivers or constraints.
4. **List at least two real options**, including "do nothing / status quo" when relevant, with honest pros and cons.
5. **Status** starts as `proposed`; becomes `accepted` after agreement. Never edit an accepted ADR's decision — supersede it with a new ADR and set the old one's status to `superseded by NNNN`.
6. **Link** related ADRs and the design doc or issue.

## Template (MADR)

```markdown
# NNNN. <Decision title>

- Status: proposed | accepted | rejected | deprecated | superseded by [NNNN](NNNN-title.md)
- Date: YYYY-MM-DD
- Deciders: <names or roles>

## Context and problem statement
<2–5 sentences: the situation and the question being decided.>

## Decision drivers
- <quality attribute / constraint, e.g. "p99 read latency < 50 ms">
- <team skills, cost, compliance, deadline>

## Considered options
1. <Option A>
2. <Option B>
3. <Option C>

## Decision outcome
Chosen option: "<Option X>", because <justification tied to the drivers>.

### Consequences
- Good: <what becomes easier>
- Bad: <what becomes harder, new risks, costs accepted>
- Follow-ups: <work this decision creates>

### Confirmation
<How compliance with this decision will be checked: review, fitness function, test, lint rule.>

## Pros and cons of the options
### <Option A>
- Good, because …
- Bad, because …
### <Option B>
- …

## More information
<links: design docs, benchmarks, related ADRs>
```

## Quality bar

- One decision per ADR. Under ~2 pages.
- Drivers are specific and measurable where possible.
- The rejected options are described fairly; a reader should see why a reasonable person might have chosen them.
- Consequences include the negative ones.
