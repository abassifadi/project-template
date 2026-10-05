# Project instructions

<!-- The <placeholders> below are filled in automatically by /phase-4-setup. -->

## Project
- **Name:** <project name>
- **What it is:** <one sentence — see docs/spec/PRODUCT_SPEC.md>
- **Stack:** <languages, frameworks, database — see docs/adr/>

## Commands
- Install: `<command>`
- Run locally: `<command>`
- Test (all): `<command>`
- Test (one file): `<command> <path>`
- Lint / format / type-check: `<command>`

## How we work: the development cycle
This project follows a phased cycle. Each phase is a command. Run `/next-step` at any time to see where we are.

1. `/phase-1-spec` → `docs/spec/PRODUCT_SPEC.md`
2. `/phase-2-architecture` → `docs/architecture/`, `docs/adr/`, `docs/api/`, `docs/security/`
3. `/phase-3-plan` → `docs/plan/BACKLOG.md`
4. `/phase-4-setup` → codebase, tooling, CI
5. `/phase-5-build [T-xxx]` → one task per run: tests first, code, review, commit
6. `/phase-6-verify [milestone]` → `docs/release/VERIFY-<milestone>.md`
7. `/phase-7-release [version]` → changelog, tag, deploy

## Rules
- The spec and the architecture docs are the source of truth. If code needs to differ from them, update the doc (or add an ADR) in the same change.
- Every change is tied to a backlog task (T-xxx). Keep `docs/plan/BACKLOG.md` status up to date.
- Write tests first for acceptance criteria. Never call work done until the tests pass, and report the command you ran.
- Get an independent review from the `code-reviewer` agent before committing. Use the `security-auditor` agent for auth, input, upload, payment, or personal-data changes.
- Conventional Commits, small commits, no secrets in the repo.
- Ask the user before pushing, opening pull requests, deploying, or creating cloud resources.
