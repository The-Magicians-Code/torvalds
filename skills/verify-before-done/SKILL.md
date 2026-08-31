---
name: verify-before-done
description: Use before claiming any coding task complete, opening a PR, or reporting success — especially when tired, when the change "obviously works", when tests seem overkill, or when only the happy path was exercised.
---

# Verify Before Done

## Overview

"Any problem caught by the computer is a problem which will not afflict a user later on."
Completion is a claim backed by tool output, not a feeling. Every gate below either
passes or gets reported — never assumed.

**Violating the letter of these gates is violating their spirit.**

## The Gates

Discover the project's actual commands first (CLAUDE.md, README, CI config, Makefile,
package scripts). The gates are invariants; the commands are the project's.

1. **Build clean** — the project compiles/typechecks with **zero new warnings**.
   Never silence a warning without understanding its real cause.
2. **Formatter run** — on every touched file, using the project's formatter config.
3. **Lint / static analysis clean** — run the project's linters; fix findings with
   judgment (understand each; don't blindly obey, don't blindly ignore).
4. **Tests run and pass** — including **failure paths**: "untested code tends to be
   broken code," and error-handling paths are untested by default. Exercise new error
   branches (force failures, bad inputs), not just the happy path. Add tests for new
   behavior in the project's existing framework.
5. **Docs updated** — any user-facing change (API, config option, CLI flag, interface)
   ships its documentation in the same change.
6. **Self-review the diff** — read the full diff as a hostile reviewer: leftover debug
   code, unrelated changes, missing includes/imports (never rely on transitive ones),
   every gate above actually done.
7. **Current base** — verify against the up-to-date target branch, not a stale checkout
   (the kernel's "test against linux-next" rule). Rebase/merge first if behind.

## Reporting

- Gates all pass → state what was run and what it showed, plainly.
- A gate fails → report the failure with output. A failed gate reported honestly is a
  successful outcome of this skill; a hidden one is not.
- A gate is impossible (no test runner, can't build here) → say so explicitly.
  "Maintainers waste too much time analyzing unverified reports and untested fixes."

## Rationalization table

| Excuse | Reality |
|---|---|
| "Trivial change, can't break anything" | Trivial changes break builds daily. The gates take minutes. |
| "Tests pass locally, that's enough" | On a stale base, against old deps? Gate 7 exists for this. |
| "The warning was pre-existing-ish" | New warnings on touched lines are yours. Zero new warnings. |
| "Error path is unreachable in practice" | Untested code is presumed broken. Force-fail it or say it's untested. |
| "I'll document in a follow-up" | Docs in the same change or the change isn't done. |
| "I ran out of budget for verification" | Then the task is not complete. Report it incomplete. |

## Red flags — STOP, you're about to lie

- Writing "should work" / "this fixes" without having run anything
- Skipping the test suite because it's slow
- The phrase "done, but I didn't get to run…" buried at the end of a report
- Claiming done with a known-failing gate

## References

- [kernel-submit-checklist.md](references/kernel-submit-checklist.md) — the kernel's
  full pre-submission checklist (`submit-checklist.rst`, `5.Posting.rst`) as the worked
  example of a maximal gate list.
- See also: **patch-discipline** (each commit must pass these gates individually) and
  **ai-authorship** (reporting what could not be verified).
