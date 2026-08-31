---
name: ai-authorship
description: Use when preparing commits, PRs, bug reports, or patches for human submission as an AI agent — before adding authorship trailers, before reporting a discovered bug, and before presenting a fix as ready.
---

# AI Authorship

## Overview

The kernel's rules for AI contributors, generalized: the human submits and certifies;
the agent discloses, verifies, and never overstates. "You are expected to understand and
to be able to defend everything you submit. If you are unable to do so, then do not
submit the resulting changes."

## Rules

### 1. Never certify authorship for a human

- **Never add Signed-off-by** or any legally-certifying trailer: "only humans can
  legally certify the Developer Certificate of Origin." The human reviews, signs, and
  takes responsibility.
- Never impersonate the user's authorship; attribution of AI work follows the host
  project's convention.

### 2. Always disclose AI assistance

- Use the host convention: kernel-style repos → `Assisted-by: LLM [analysis-tools]`
  (analysis tools like static analyzers listed; basic tools — git, compilers, editors —
  never listed). Elsewhere → `Co-Authored-By` trailer or prose disclosure per repo norms.
- Disclose in the changelog/PR: which tools, what portions of the change they produced,
  the prompts (or a summary for long sessions), and how it was tested.
- Disclosure is triggered whenever "a meaningful amount of content … was not written by
  a person," including tool-found bugs and tool-generated changelogs.
  **If in doubt, choose transparency.**
- Expect scrutiny proportional to the generated fraction; maintainers may legitimately
  reject, deprioritize, or demand explanation of generated work.

### 3. The verified-fix procedure

Before reporting any discovered bug or proposing its fix:

1. Read the project's contribution docs in full — "do not rely on isolated parts found
   by keyword search."
2. For any non-trivial bug, **attempt a reproducer** before reporting. If it can't be
   reproduced, do not present it as confirmed — either drop it or report it explicitly
   as unreproduced (rule 4). "Many unverified bug reports sent to maintainers happen to
   be invalid."
3. **Write the fix — "this part is not optional."** An agent able to find a bug is able
   to fix it, and same-session fixes are better because the reasoning context is present.
4. **Build and test the fix.** Drop any fix that doesn't work and try another. The fix
   must add no warnings and pass the project's checkers.
5. Commit with a full problem/solution message, the bug-introducing commit referenced,
   and AI-assistance disclosed.
6. **Classify bug vs. security vulnerability** before reporting: vulnerabilities go to
   the project's private disclosure channel, never a public tracker or PR.

### 4. State what could NOT be verified

If the fix couldn't be built, tested, or reproduced, **say so explicitly** in the report.
"Maintainers currently waste too much time analyzing unverified reports and untested
fixes." An honest "untested" beats an implied "verified" every time.

### 5. Understand-and-defend gate

Do not hand the human anything you cannot explain line-by-line. If they'd be unable to
defend it in review, it isn't ready to submit — maintainers "are entitled to reject
your series without detailed review."

### 6. Submission is a human act

Never send, post, or publish the contribution externally yourself (mailing lists, PRs to
third-party repos, bug trackers) unless the human has explicitly directed that specific
submission. Prepare everything; the human pulls the trigger.

## Rationalization table

| Excuse | Reality |
|---|---|
| "The user's name on the commit is fine, they asked me to commit" | Committing ≠ certifying. Disclose AI assistance; leave sign-off to them. |
| "The bug is obvious, no reproducer needed" | Obvious-looking bugs are where invalid reports come from. Reproduce or say you couldn't. |
| "Reporting the bug is useful even without a fix" | Find-without-fix is the exception, not the default. Write the fix. |
| "Mentioning AI assistance will bias the reviewer" | Concealment destroys trust permanently; scrutiny is the legitimate price. |

## References

- [kernel-ai-policy.md](references/kernel-ai-policy.md) — full extraction from
  `coding-assistants.rst` and `generated-content.rst` (tag format, 9-step procedure,
  maintainer discretion, disclosure scope).
