---
name: debugging-systematically
description: Finds the root cause of bugs, crashes, failing tests, and production incidents using a reproduce–hypothesize–bisect–verify loop instead of guessing. Use when something is broken, a test fails, an error or stack trace appears, behavior is unexpected, or the user says "why does this happen".
metadata:
  role: senior-developer
  version: "1.0"
---

# Debugging systematically

Never change code until you can explain the bug. A fix without a root cause is a guess.

## Workflow

```
Debug progress:
- [ ] 1. Reproduce reliably
- [ ] 2. Gather evidence
- [ ] 3. Form ranked hypotheses
- [ ] 4. Test hypotheses (cheapest first)
- [ ] 5. Fix the root cause
- [ ] 6. Prove it with a regression test
- [ ] 7. Look for siblings
```

**1. Reproduce.** Write the exact steps, inputs, environment, and the expected vs actual result. Reduce to the smallest failing case. If you cannot reproduce, gather more evidence before touching code.

**2. Evidence.** Read the *full* error and stack trace, top to bottom. Check logs around the timestamp, recent changes (`git log -p --since`, deploy history), config and environment differences, dependency versions.

**3. Hypotheses.** List 2–5 possible causes, each with a test that would confirm or kill it. Rank by likelihood × cheapness to check.

**4. Test.** Change one variable at a time. Useful techniques:
- **Bisect history:** `git bisect start <bad> <good>` then `git bisect run <test-cmd>`.
- **Bisect code/data:** halve the input or comment out half the pipeline until the failure flips.
- **Add assertions/logging** at boundaries to find the first point where state is wrong.
- **Debugger** with a conditional breakpoint at the earliest wrong state.
- **Diff working vs broken:** environment, data, versions, flags.

**5. Fix.** Fix where the wrong state is *created*, not where it is *noticed*. Keep the fix minimal; separate refactors into another change.

**6. Prove.** Add a test that fails before the fix and passes after. Run the full suite.

**7. Siblings.** Search for the same pattern elsewhere (`grep` for the faulty call or idiom). Ask: why did tests, types, or review not catch this? Recommend that guard.

## Production incidents

Mitigate first (rollback, feature flag off, scale up, failover), then debug. Record a timeline as you go. Afterwards write a blameless postmortem: impact, timeline, root cause, contributing factors, action items with owners.

## Report format

```markdown
**Symptom:** <what was observed>
**Root cause:** <the actual defect, with file:line>
**Why it happened:** <the chain from cause to symptom>
**Fix:** <what changed and why it is correct>
**Proof:** <test name + command + result>
**Prevention:** <guard that would catch this class of bug>
```
