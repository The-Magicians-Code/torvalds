---
name: coding-style
description: Use when writing, editing, or reviewing any code — before typing the first line, and whenever tempted to hand-format, add a clever one-liner, write a long function, or "clean up" style in code you aren't otherwise changing.
---

# Coding Style

## Overview

Uniform style exists so any developer can quickly understand any part of a large codebase.
It is enforced socially before technically: reviewers refuse to read non-conforming code.
Kernel rule of thumb: "Kernel coding style is super simple. Avoid tricky expressions."

## Rules

### 1. Formatting is the formatter's job

- Discover and run the project's formatter (`.clang-format`, `.editorconfig`, prettier,
  gofmt, rustfmt, black, …) on every file you touch. Never hand-format against it.
- Never add editor modelines or personal formatting config to source files.
- Match the file you're editing when the project has no formatter: same indentation,
  brace style, line-length, import ordering as the surrounding code.

### 2. Naming

- Global (exported, public) names must be descriptive: `count_active_users()`, never `cntusr()`.
  "To call a global function `foo` is a shooting offense."
- Local names short and plain: a loop counter is `i`, a temporary is `tmp`. If you fear
  ambiguity among locals, the function is too big — split it, don't lengthen names.
- Never encode types into names (Hungarian notation): the compiler knows the types.
- Action-named functions return success/error; predicate-named functions return boolean truth.
  Mixing these conventions is "a fertile source of difficult-to-find bugs."

### 3. Functions small, nesting shallow

- A function does one thing and fits on one or two screens. More than 3 levels of
  nesting means the design is wrong — extract helpers with descriptive names.
- Keep local state small (the kernel's heuristic: 5–10 locals; "a human brain can
  generally easily keep track of about 7 different things").

### 4. No clever constructs

- One statement per line; no hidden side effects; no expressions that require re-reading.
- No helpers/macros that hide control flow (a call that secretly returns from the caller).
- Never split user-visible strings (log/error messages) across lines — it breaks grep.
- Use the standard library / project utility instead of re-inventing it
  (the kernel's `ARRAY_SIZE()`/`min()`/`max()` rule): search existing helpers first.

### 5. Comments: why, never how

- Never explain HOW code works — rewrite the code until the how is obvious.
- Do comment: the subtle, the dangerous, ordering/locking constraints, and anything a
  future "cleanup" might wrongly simplify ("why it is done the way it is").
- Prefer executable assertions to comments: `assert`/`lockdep_assert_held`-style checks
  fail loudly; comments can't.
- Needing comments inside a function body is a sign the function is too complex.

### 6. Know the banned constructs

- Every ecosystem has its `strcpy`/`volatile`/VLA equivalents: unsafe or deprecated APIs
  with mandated replacements. Learn the project's deny-list (lint config, deprecation
  warnings, docs) and use the replacements. Never silence a deprecation warning.
- Overflow-prone arithmetic in allocation/size computations gets the checked helpers.

### 7. Style churn is noise

- Pure style-fix changes to code you aren't otherwise working on "get a chilly reception."
  Fix style incidentally in code you're touching for another reason; never generate
  reformat-only diffs unless explicitly asked.
- Style rules bend to readability, but a deliberate deviation carries a comment or
  commit-message line stating why; an unjustified deviation is a violation.

## Red flags — stop and reconsider

- About to manually align/indent instead of running the formatter
- A function that needs a scroll to read, or a 4th nesting level
- A comment starting with "This loop iterates over…" (how, not why)
- A diff that only reorders/reformats untouched code
- Reaching for a deprecated API "just this once"

## References

- [kernel-style-rules.md](references/kernel-style-rules.md) — the full extracted
  rule→rationale→mechanical-check tables from `coding-style.rst`, `deprecated.rst`,
  `volatile-considered-harmful.rst`, `programming-language.rst`.
