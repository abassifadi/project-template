---
name: reviewing-code
description: Reviews code changes (diffs, pull requests, branches) for correctness, security, design, tests, and maintainability, producing prioritized findings with file:line references. Use when the user asks for a code review, PR review, "look over my changes", or before merging.
metadata:
  role: senior-developer
  version: "1.0"
---

# Reviewing code

Review like the owner of the code who will be paged when it breaks. Find real defects first; style comes last.

## Workflow

Copy this checklist and track progress:

```
Review progress:
- [ ] 1. Understand intent (PR description, linked issue, commit messages)
- [ ] 2. Read the full diff and the surrounding code it touches
- [ ] 3. Correctness pass
- [ ] 4. Security pass
- [ ] 5. Design and maintainability pass
- [ ] 6. Tests pass
- [ ] 7. Verify each finding, then report
```

**1. Intent.** State in one sentence what the change is supposed to do. If you cannot, ask before reviewing.

**2. Context.** Run `git diff <base>...HEAD` (or read the PR). Open callers and callees of every changed function. A diff alone hides most bugs.

**3. Correctness.** For each changed function ask:
- What inputs break it? (empty, null, zero, negative, huge, unicode, concurrent calls)
- Are errors handled, propagated, or silently swallowed?
- Are resources released on every path (files, locks, connections, transactions)?
- Are there off-by-one, race, ordering, or timezone issues?
- Does it change a public contract (API, schema, config, event) without migration?

**4. Security.** Check untrusted input → sink paths: SQL/NoSQL/shell/template injection, path traversal, SSRF, deserialization, authz checks on every new endpoint, secrets in code or logs. For deep work, use the `securing-code` skill.

**5. Design.** Is the change in the right layer? Duplicated logic that already exists? New abstraction justified by at least two real uses? Names that say what, not how?

**6. Tests.** Does a test fail without the change? Are edge cases from step 3 covered? Are tests deterministic (no sleeps, real clocks, network)?

**7. Verify.** For every finding, re-read the code and construct a concrete failure scenario (input → wrong output). Drop findings you cannot make concrete or mark them as questions.

## Severity

| Level | Meaning | Merge? |
|-------|---------|--------|
| **Blocker** | Bug, data loss, security hole, broken contract | No |
| **Major** | Likely bug under realistic conditions, missing tests for risky logic | Fix first |
| **Minor** | Maintainability, clarity, small inefficiency | Author's call |
| **Nit** | Style or preference | Optional; prefix with "nit:" |

## Output format

```markdown
## Summary
<one paragraph: what the change does, overall verdict (approve / request changes)>

## Findings
1. **[Blocker] <short title>** — `path/file.ext:42`
   <what is wrong> → <concrete failure scenario> → <suggested fix>
2. ...

## Questions
- <things you could not determine>

## What's good
- <one or two specific strengths, if any>
```

Rules:
- Rank findings most severe first. Cap nits at five.
- Always give `file:line`. Quote code only when it clarifies.
- Suggest a fix, not just a problem. Prefer the smallest correct fix.
- Do not report formatting that a linter/formatter would catch; recommend the tool instead.
