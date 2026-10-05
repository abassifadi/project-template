# Playbook: from idea to release, step by step

This walks through one imaginary project, **"VetBook"** (pet owners book vet appointments online),
to show what you type, what Claude does, and what you check at each step. The example outputs are
shortened.

---

## Day 0 — Create the project

```bash
# On GitHub: "Use this template" → new repo "vetbook", then:
git clone https://github.com/<you>/vetbook.git
cd vetbook
claude
```

---

## Phase 1 — Spec

**You type:**
```text
/phase-1-spec A web app where pet owners book vet appointments online and clinics manage their calendar
```

**Claude asks** (first of up to three short rounds):
> 1. Who are the users: only pet owners and clinic staff, or also vets themselves?
> 2. Is it one clinic or many clinics (a marketplace)?
> 3. Must-haves for v1: booking, cancelling, reminders? Payments?
> 4. Expected size: how many clinics and bookings per day in year one?
> 5. Any regulations or countries to consider (e.g. GDPR in the EU)?

**You answer**, for example: "Single clinic first, owners and staff only, no payments in v1, EU users,
about 200 bookings a day, email reminders are a must."

**Claude writes** `docs/spec/PRODUCT_SPEC.md`. Here's a piece of it:

```markdown
### F1. Book an appointment — Priority: Must
**US-1.1** As a pet owner, I want to pick a free slot so that I don't have to phone the clinic.
- AC-1.1.1 Given a slot is free, when I book it, then it is reserved for me and I get a confirmation email within 1 minute.
- AC-1.1.2 Given a slot was just taken by someone else, when I try to book it, then I see "slot no longer available" and other free slots.
```

**The spec-reviewer agent** then reports, for example:
> *Untestable:* NFR-1 "the app should be fast" → suggest "95% of pages load in < 2 s".
> *Edge case:* what happens when the clinic cancels a booking? No story covers it.

Claude fixes what it can and asks you the rest.

✋ **Your checkpoint:** read the summary and the spec. Reply "approved", or ask for changes.

---

## Phase 2 — Architecture

**You type:** `/phase-2-architecture`

**Claude proposes options**, for example:

| | A. Modular monolith (recommended) | B. Frontend + backend API + workers |
|---|---|---|
| Fits 200 bookings/day | ✅ easily | ✅ |
| Team of 1–3 | ✅ simple | ⚠️ more moving parts |
| Cost | Low | Medium |

It asks you to choose the stack, for example: "TypeScript + Next.js + PostgreSQL, hosted on Render or
Fly.io? Or Python + Django? Which does your team know?"

**Claude creates:**
- `docs/architecture/ARCHITECTURE.md`: the design, with context and container diagrams
- `docs/adr/0001-use-modular-monolith.md`, `0002-use-postgresql.md`, `0003-use-nextjs.md`, `0004-email-login-with-magic-links.md`
- `docs/architecture/DATA_MODEL.md`: the Owner, Pet, Clinic, Slot, and Booking tables. A unique constraint on (slot, active booking) prevents double booking.
- `docs/api/openapi.yaml`: the booking endpoints
- `docs/security/THREAT_MODEL.md`: for example, "T3: an owner reads other owners' bookings by changing the ID in the URL → check ownership on every request"

**The architecture-reviewer agent** flags, for example:
> *High:* no database backups defined → add daily backups with a tested restore.

✋ **Your checkpoint:** approve the architecture. The ADRs are marked `accepted`.

---

## Phase 3 — Plan

**You type:** `/phase-3-plan`

**Claude creates** `docs/plan/BACKLOG.md`:

```markdown
## M0 — Project setup
- [ ] T-000 Project setup (run /phase-4-setup)

## M1 — Walking skeleton
- [ ] T-001 Owner signs in with a magic link and sees an empty "My bookings" page
  - Covers: AC-4.1.1 · Depends on: T-000
- [ ] T-002 Deploy to a staging environment through CI
  - Covers: NFR-2

## M2 — MVP
- [ ] T-003 Clinic staff create available slots          (AC-2.1.1, AC-2.1.2)
- [ ] T-004 Owner books a free slot                      (AC-1.1.1)
- [ ] T-005 Prevent double booking under concurrency     (AC-1.1.2, threat T5)
- [ ] T-006 Confirmation and reminder emails             (AC-3.1.1, AC-3.2.1)
- ...
```

✋ **Your checkpoint:** check the order and priorities, then approve.

---

## Phase 4 — Setup

**You type:** `/phase-4-setup`

**Claude:**
- generates the Next.js project and adds ESLint, Prettier, TypeScript strict mode, Vitest, and Playwright
- adds `docker-compose.yml` for PostgreSQL, a migration tool, `.env.example`, and a GitHub Actions CI pipeline
- fills in `CLAUDE.md` with the real commands (`npm run dev`, `npm test`, …)
- runs a smoke test and shows you the result:
  ```text
  ✓ GET /api/health returns 200   (1 passed)
  ```
- commits `chore: project setup` and marks T-000 done

It **asks before** pushing to GitHub or connecting a hosting account.

---

## Phase 5 — Build (repeat for every task)

**You type:** `/phase-5-build`. It picks T-001 automatically. Or name a task: `/phase-5-build T-004`.

**What you see at the end of each run:**

```markdown
**Done:** T-004 Owner books a free slot
**Acceptance criteria covered:** AC-1.1.1 (tests: tests/booking/book-slot.test.ts)
**Tests:** npm test → 38 passed
**Review:** code-reviewer found 1 Major (missing ownership check on GET /bookings/:id) → fixed.
            security-auditor: PASS.
**Next task:** T-005 Prevent double booking under concurrency → run `/phase-5-build`
```

**Tips for this phase:**
- Start a **new conversation** every few tasks. The backlog remembers where you are.
- If Claude asks a product question ("Can owners book for two pets in one slot?"), the spec didn't
  say. Answer it, and Claude adds the answer to the spec.
- Hit a bug along the way? Claude switches to `debugging-systematically` on its own. You can also say
  "debug this".

---

## Phase 6 — Verify

When every task in M2 is `[x]`:

**You type:** `/phase-6-verify M2`

**Claude produces** `docs/release/VERIFY-M2.md`:

```markdown
Decision: NO-GO

| Check | Result |
|-------|--------|
| Test suite | ✅ 142 passed, coverage 84% |
| Acceptance criteria (qa-tester) | ❌ 22/23 — AC-1.1.2 shows an error page instead of other free slots |
| Security (security-auditor) | ✅ 0 high · 1 medium (rate-limit magic-link requests) |
| Performance NFR-1 | ✅ p95 page load 1.3 s (target 2 s) |

Follow-up tasks created: T-015 (fix AC-1.1.2), T-016 (rate limiting)
```

Run `/phase-5-build` for T-015 and T-016, then `/phase-6-verify M2` again → **GO**.

---

## Phase 7 — Release

**You type:** `/phase-7-release 1.0.0`

**Claude:**
1. Checks production readiness: health check, logs, alerts on error rate and latency, and `docs/release/RUNBOOK.md` with rollback steps.
2. Writes `CHANGELOG.md` from the commits:
   ```markdown
   ## 1.0.0
   ### Added
   - Online booking of vet appointments (T-004)
   - Email confirmations and reminders (T-006)
   ### Security
   - Rate limiting on sign-in links (T-016)
   ```
3. Shows you the version, the changelog, and the rollback plan, and **waits for your "yes"**.
4. Tags `v1.0.0`, deploys through the pipeline, and watches the error rate and response times afterwards.

---

## After launch

| You want to… | Do this |
|--------------|---------|
| Start the next milestone | `/phase-5-build` |
| Add a feature | Ask Claude to add stories to the spec (or `/phase-1-spec` to revise it), then add tasks to the backlog, then `/phase-5-build` |
| Add a big feature | "Write a design doc for video consultations" → ADRs → tasks → `/phase-5-build` |
| Fix a production bug | "Customers get a 500 error on booking since this morning" → `debugging-systematically` |
| Check where things stand | `/next-step` |

---

## Checklist of checkpoints

- [ ] Spec approved: every Must-have story has Given/When/Then criteria
- [ ] Architecture approved: stack chosen by you and recorded in ADRs; threat model exists
- [ ] Backlog approved: the walking skeleton comes first; tasks are about a day each
- [ ] Setup: install, lint, test, and run all work; CI is green
- [ ] Each task: tests pass, review clean, committed, backlog updated
- [ ] Verification report says GO
- [ ] Release approved by you; rollback plan exists
