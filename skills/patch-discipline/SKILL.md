---
name: patch-discipline
description: Use when committing work, splitting a change into commits, writing commit messages, or preparing a branch/PR — especially when a change grew to touch several concerns, or a "small fix" picked up drive-by cleanups.
---

# Patch Discipline

## Overview

A commit is a unit of review, revert, and bisection — not a save point. Its message is
"the first documentation of any patch," read by reviewers, backporters, and future bug
hunters who cannot ask you questions.

## Splitting: one logical change per commit

- One conceptual change per commit, even if it touches many files. Multiple concepts in
  one file get split.
- **Never mix change types**: bug fix, cleanup/refactor, formatting, and feature are
  separate commits. A fix buried in a cleanup is unreviewable and unbackportable.
- Heuristic: "if your description starts to get long, that's a sign that you probably
  need to split up your patch."
- Split by logical independence, not development chronology. Don't over-split into
  absurd granularity either.
- **Every commit builds and passes tests on its own** (bisectability). No transiently
  broken intermediate states; state dependencies between commits explicitly.
- Refactor-then-change: an API/internal change lands as its own commit that also updates
  every caller atomically — never half-migrated states.
- A multi-commit branch/PR gets a cover description (PR body): motivation, structure of
  the series, how it was tested.

## Commit messages

Format — adopt the repo's existing prefix convention (conventional commits etc.) if one
exists; otherwise kernel style:

```
area: imperative summary under ~72 chars

Context: what the world looked like and why that was a problem.
Problem: the user-visible impact (crash, wrong result, perf, security).
Solution: what this change does and why this approach.
```

Rules:
- **Imperative mood**: "make X do Y", never "this patch makes" / "I changed" / "we fix".
- **Why over what**: the diff shows what changed; the message must argue why it's worth
  applying. "A surprising number of developers fail to provide that information."
- **Self-contained**: no "see the discussion" / "as described in the ticket" as the only
  explanation. Link the ticket AND summarize it.
- **Quantify claims**: performance, memory, or size claims come with numbers.
- Reference prior commits as `abcdef123456 ("subject line")` — hash and title, so the
  reference survives rebases of everything else.
- Bug fixes name the commit that introduced the bug (`Fixes:`-style trailer or prose)
  and link the report being closed.
- Function names written as `function_name()`; error/log output quoted verbatim so it's
  searchable.
- Ephemeral notes (what changed since review round 1, CI links) go in the PR
  conversation, not in permanent commit history.

## Review responses

- Answer **every** review comment; "ignoring reviewers is a good way to get ignored in
  return." Fix it, or explain — and per Andrew Morton: "every review comment which does
  not result in a code change should result in an additional code comment instead."
- Push back only on real technical grounds; if others side with the reviewer, rethink.
- Style/maintainability requests are legitimate — they're about the next ten years, not
  pedantry.
- Each new revision states how each comment was handled; reviewers "should not have to
  search through archives to familiarize themselves with what was said last time."
- After a substantial rewrite, previously-given approvals are stale — flag that
  re-review is needed rather than carrying approval forward.

## Rationalization table

| Excuse | Reality |
|---|---|
| "The cleanup is tiny, I'll fold it in" | Tiny cleanups hide fixes from reviewers and backporters. Separate commit. |
| "Message can be short, the diff is clear" | The diff never explains why. That's the message's only job. |
| "I'll squash it all at the end" | Squashing destroys bisectability and per-change rationale. Split as you go. |
| "Reviewer's nitpick, ignoring it" | Unanswered comments stall merges and burn trust. Answer everything. |

## References

- [kernel-submission-rules.md](references/kernel-submission-rules.md) — full extraction
  from `submitting-patches.rst`, `5.Posting.rst`, `6.Followthrough.rst`, `maintainer-tip.rst`
  (tag taxonomy, ordering, series mechanics, changelog before/after examples).
- See also: **verify-before-done** (the gate set every individual commit must pass) and
  **ai-authorship** (disclosure trailers; submission is a human act).
