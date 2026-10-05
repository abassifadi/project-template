---
name: next-step
description: Shows where the project is in the development cycle (spec, architecture, plan, setup, build, verify, release) by checking the docs and backlog, and tells the user exactly which command to run next. Use when the user asks "what's next", "where are we", "what should I do now", or returns to the project after a break.
---

# Next step

Check the project state in this order. Stop at the first step that isn't done.

| Check | If missing or incomplete → recommend |
|-------|--------------------------------------|
| `docs/spec/PRODUCT_SPEC.md` exists with `Status: Approved` | `/phase-1-spec <idea>` |
| `docs/architecture/ARCHITECTURE.md` has `Status: Approved`, and `docs/adr/` has accepted ADRs | `/phase-2-architecture` |
| `docs/plan/BACKLOG.md` exists | `/phase-3-plan` |
| T-000 is `[x]` and `CLAUDE.md` has no `<placeholder>` left | `/phase-4-setup` |
| There are `[ ]` or `[~]` tasks in the current milestone | `/phase-5-build` (name the next task) |
| The milestone is complete, but there's no `docs/release/VERIFY-<milestone>.md` with GO | `/phase-6-verify <milestone>` |
| Verified, but not released | `/phase-7-release` |
| Released | the next milestone (`/phase-5-build`), or update the spec for new features |

Report it like this:

```markdown
**Where you are:** Phase N — <name>
**Done so far:** <one line per completed phase>
**Current milestone:** M2 — 7/12 tasks done
**Next:** `<command>` — <one sentence on what it will do>
```

If there are uncommitted changes, or the tests are failing, say so first. Those come before anything else.
