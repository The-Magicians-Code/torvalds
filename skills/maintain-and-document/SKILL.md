---
name: maintain-and-document
description: Use when refactoring, changing or removing an API, deleting code, adding abstraction, duplicating logic, or touching documentation — and whenever a change might alter behavior existing users depend on.
---

# Maintain and Document

## Overview

Codebases are maintained for decades by people who weren't there. Two doctrines anchor
everything: never break existing users, and keep the internals fluid enough to fix.

## The regressions rule

- A regression is a change that breaks something for **existing users**. Regressions get
  reverted, not argued: "we don't fix bugs by introducing new problems. That way lies
  madness." No net-benefit arithmetic ("helps 10 users per 1 broken") — the broken user
  wins.
- If your change caused a regression, fixing it outranks all other work.

## Two tiers of interface, opposite rules

- **Public / user-facing interfaces are frozen the moment they ship.** "Once an
  interface has been exported to user space, it must be supported indefinitely." So they
  must be right the first time: extra thought, wide review, documentation before release.
  An *intentional* break requires an announced deprecation window, a documented
  migration path, and a version signal — unannounced breakage is exactly the regression
  the rule above bans.
- **Internal interfaces are deliberately fluid.** Change them freely when the need
  arises, under four conditions:
  1. Justify the change in its own right.
  2. Land it as its own commit, not buried in other work.
  3. **You fix every caller in the same change** — the whole codebase stays working.
  4. Make incompatible changes **fail loudly at build/type-check time**, so no caller
     is silently missed.
- Working around a bad internal API instead of fixing it is the wrong move: "you are
  empowered to make such changes."

## Code hygiene

- **Delete unused code and interfaces.** "Unused interfaces are pretty much impossible
  to test for validity" — dead code rots and misleads.
- **No premature abstraction**: "abstraction should be used to the level required and no
  further." An always-default parameter is probably already broken because nothing
  exercises it; remove unused flexibility.
- **Extract duplication**: significant copying between modules is the signal to lift a
  shared helper. "There is no value in replicating the same code throughout."
- Design cross-cutting concerns (concurrency, error handling) in from the start —
  "retrofitting locking after the fact is a rather more difficult task."
- Prefer runtime-checkable assertions over comments for invariants ("caller must hold
  the lock" → an assertion that fires). "Comments can't do that."

## Documentation

- Docs update **in the same change** as the behavior: new interfaces, options, flags,
  and parameters ship documented or don't ship.
- **Repeated questions or repeated review corrections are documentation defects** —
  write the doc/handbook entry instead of answering again.
- Docs have a lifecycle: retire stale entries periodically, keep a uniform schema per
  entry, treat doc fixes as normal changes through normal review. The repo's docs are
  the source of truth over any external mirror.
- Comment the things a future "janitor" would wrongly clean up: why it's done this way.

## Rationalization table

| Excuse | Reality |
|---|---|
| "The break only affects an edge case" | Edge-case users are still existing users. Revert or fix. |
| "I'll migrate the remaining callers later" | Half-migrated states are where bugs live. All callers, same change. |
| "Keep the old function around just in case" | Unused code can't be tested and will rot. Delete it. |
| "Add the parameter now, we'll need it eventually" | Unused flexibility is presumed broken. Add it when needed. |
| "Docs can trail the release" | Undocumented shipped interfaces get depended on as-is, then frozen wrong. |

## References

- [kernel-maintenance-doctrine.md](references/kernel-maintenance-doctrine.md) — full
  extraction from `4.Coding.rst`, `stable-api-nonsense.rst`, `management-style.rst`,
  `maintainer-handbooks.rst`, `kernel-docs.rst` (regression policy, API philosophy,
  reversibility, docs lifecycle).
- See also: **coding-style** (assertions-over-comments in the small) and
  **verify-before-done** (docs-in-same-change as a completion gate).
