# torvalds-style

Agent skills distilled from the Linux kernel's `Documentation/process/` — three decades of
hard-won discipline about writing, verifying, committing, and maintaining code, translated
into language-agnostic SKILL files for coding agents.

## Skills

| Skill | Enforcement moment |
|---|---|
| [coding-style](skills/coding-style/SKILL.md) | Writing, editing, or reviewing any code |
| [verify-before-done](skills/verify-before-done/SKILL.md) | Before claiming any task complete or opening a PR |
| [patch-discipline](skills/patch-discipline/SKILL.md) | Committing, splitting work, writing commit messages |
| [maintain-and-document](skills/maintain-and-document/SKILL.md) | Refactoring, API changes, deleting code, touching docs |
| [ai-authorship](skills/ai-authorship/SKILL.md) | Preparing commits, PRs, or bug reports for human submission |

Each skill's `SKILL.md` carries the enforceable rules; `references/` holds the underlying
kernel-doc extractions for depth on demand.

## Design principles

- **Language-agnostic**: the kernel's C specifics (tabs=8, `volatile`, checkpatch tests) appear
  only as cited examples; the skills encode the transferable discipline.
- **Hard gates, parameterized**: skills state invariants ("zero new warnings") and instruct the
  agent to discover the host project's actual tools, never hardcoding commands.
- **Host repo wins**: where a project has its own conventions (commit prefixes, formatters),
  consistency with the host beats purity of the kernel form.

## Sources

Derived from [torvalds/linux `Documentation/process/`](https://github.com/torvalds/linux/tree/master/Documentation/process),
principally: `coding-style.rst`, `deprecated.rst`, `volatile-considered-harmful.rst`,
`programming-language.rst`, `submitting-patches.rst`, `submit-checklist.rst`,
`5.Posting.rst`, `6.Followthrough.rst`, `4.Coding.rst`, `stable-api-nonsense.rst`,
`management-style.rst`, `maintainer-tip.rst`, `maintainer-handbooks.rst`,
`kernel-docs.rst`, `coding-assistants.rst`, `generated-content.rst`.

## Install

Copy (or symlink) the directories under `skills/` into your agent's skills directory —
e.g. `~/.claude/skills/` for Claude Code — or point your runtime's skill loader at this
repo.
