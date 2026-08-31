# Kernel style rules — full extraction

Source: `Documentation/process/{coding-style,deprecated,volatile-considered-harmful,programming-language}.rst`.
Format: rule → rationale → mechanical check. C-specific; use as the worked example of
"every style rule should map to a tool that enforces it."

## Indentation & statements
| Rule | Rationale | Check |
|---|---|---|
| Tab = 8 chars; tabs, not spaces | Blocks visible "after 20 straight hours" of staring | clang-format `UseTab: Always, IndentWidth: 8`; checkpatch `CODE_INDENT` |
| >3 nesting levels = broken program | 8-char indent is a deliberate warning signal | clang-tidy cognitive-complexity |
| `case` labels in same column as `switch` | eases nesting | clang-format `IndentCaseLabels: false` |
| Never multiple statements/assignments per line | "unless you have something to hide" | checkpatch `TRAILING_STATEMENTS`, `MULTIPLE_ASSIGNMENTS` |
| No trailing whitespace; no space-indent | breaks later patch context | checkpatch; `git diff --check`; EditorConfig |

## Lines & strings
| Rule | Rationale | Check |
|---|---|---|
| ~80-col preferred limit; break sensibly unless readability suffers | tooling compat | checkpatch `LONG_LINE` |
| NEVER split user-visible strings | "breaks the ability to grep for them" | checkpatch `SPLIT_STRING` |

## Braces & spaces
| Rule | Rationale | Check |
|---|---|---|
| K&R braces; functions get opening brace on own line | "functions are special anyway (you can't nest them in C)" | clang-format `BreakBeforeBraces: Linux` |
| No braces for single statements; braces on both arms if either is multi | consistency | checkpatch `BRACES` |
| Space after control keywords, none after `sizeof`-likes, none inside parens | keyword vs function distinction | checkpatch `SPACING` |
| `*` adjacent to name (`char *p`) | — | clang-format `PointerAlignment: Right` |
| Spaces around binary ops, none around unary/`.`/`->` | — | checkpatch `SPACING` |

## Naming
| Rule | Rationale | Check |
|---|---|---|
| No CamelCase / cute names; `tmp` beats `ThisVariableIsATemporaryCounter` | C is Spartan | checkpatch `CAMELCASE` |
| Descriptive globals mandatory; short locals encouraged | — | review |
| No Hungarian notation | "the compiler knows the types anyway" | regex |
| No new `master/slave`, `blacklist/whitelist`; use primary/secondary, allowlist/denylist etc. Exceptions: existing ABI, pre-2020 specs | inclusive terminology | checkpatch --strict |

## Typedefs
Never typedef structs/pointers. Allowed only: opaque accessor-only objects (`pte_t`),
reasoned abstract integer types, sparse type-checking types, `u8/u16/u32/u64`,
userspace-shared `__u32` types. "NEVER EVER use a typedef unless you can clearly match
one of those rules." — checkpatch `NEW_TYPEDEFS`.

## Functions
| Rule | Rationale | Check |
|---|---|---|
| Short, one thing, 1–2 screenfuls; length inversely proportional to complexity | — | metrics |
| ≤5–10 locals | "about 7 different things" | AST |
| Named parameters in prototypes; no `extern` on declarations | reader info | checkpatch `FUNCTION_ARGUMENTS`, `AVOID_EXTERNS` |
| `EXPORT_SYMBOL()` immediately after closing brace | — | checkpatch |

## Centralized exit (goto)
goto for common cleanup on multi-exit functions; labels named for what they do
(`out_free_buffer:`, never `err1:`); avoid the "one err bug" (one label freeing
possibly-NULL state from multiple paths); simulate errors to test all exit paths.

## Comments
WHAT/WHY at function head, never HOW; intra-body comments signal over-complexity;
kernel-doc for public APIs, no boilerplate restating the signature; block-comment style
enforced by checkpatch `BLOCK_COMMENT_STYLE`; one declaration per line to leave room for
per-item comments.

## Macros & preprocessor
| Rule | Rationale | Check |
|---|---|---|
| CAPS constants; inline functions over function-like macros | double-evaluation, type checks | checkpatch `MACRO_ARG_REUSE` |
| Multi-statement macros in `do { } while (0)` | — | checkpatch |
| No macros with hidden control flow, magic-name locals, or l-value args | "don't break the internal parsers of those who read the code" | checkpatch `MACRO_WITH_FLOW_CONTROL` |
| Parenthesize macro expressions | precedence | checkpatch `COMPLEX_MACRO` |
| Avoid `#ifdef` in .c: header stubs + `IS_ENABLED()`; compile out whole functions; comment long `#endif`s | compiler still type-checks the code | checkpatch `PREFER_IS_ENABLED` |

## Messages, allocation, crashes
| Rule | Rationale | Check |
|---|---|---|
| Correct spelling; no `(%d)`; right log level; working drivers are quiet | — | checkpatch `TYPO_SPELLING`, `PREFER_PR_LEVEL` |
| `sizeof(*p)` never `sizeof(struct foo)`; no cast of allocator return; array allocators over open multiplication; no OOM message (allocator already warns) | overflow + type-change bugs | checkpatch `ALLOC_SIZEOF_STRUCT`, `ALLOC_WITH_MULTIPLY`, `OOM_MESSAGE` |
| No `inline` on >3-line functions or single-use statics | icache bloat; "a pagecache miss ... easily takes 5 milliseconds" | checkpatch `INLINE` |
| No new `BUG()`/`BUG_ON()`; `WARN_ON_ONCE()` + recovery; never WARN on user-triggerable conditions; `BUILD_BUG_ON()` encouraged | crashing is the user's decision | checkpatch `AVOID_BUG` |

## bool & return conventions
`true/false` not `1/0`; no `!!`; no bool struct members where layout matters (alignment
varies); many bool args → flags word. Action names → 0/-Exxx; predicate names → boolean.

## Deprecated constructs (deny-list pattern)
| Banned | Replacement | Why |
|---|---|---|
| `strcpy`/`strncpy`/`strlcpy` | `strscpy` family | overflow / no-NUL / over-read |
| `simple_strtol` etc. | `kstrto*` | ignores overflow |
| open-coded `count * size` in allocators | `kmalloc_array`, `struct_size()`, `size_mul()` | multiply overflow → heap overflow |
| VLAs | fixed arrays / heap | stack overflow; bad codegen (`-Wvla`) |
| implicit fallthrough | `fallthrough;` — every case ends in break/fallthrough/continue/goto/return | CWE-484 (`-Wimplicit-fallthrough`) |
| zero/one-element trailing arrays | C99 flexible array members + `struct_size()` | bounds-checkable |
| `%p` printing | `%pS` / removal / justified `%px` | address exposure |
| `volatile` on shared data | proper locking/barriers; accessors for MMIO; `cpu_relax()` busy-wait | volatile suppresses optimization, doesn't provide atomicity; "if volatile is still necessary, there is almost certainly a bug" |

## Tooling stack (the model to replicate per-ecosystem)
formatter (`clang-format`, `.editorconfig`) → style linter (`checkpatch.pl --strict`) →
compiler warnings (`-Wall -Werror -Wvla -Wimplicit-fallthrough`) → static analysis
(sparse `make C=1`) → semantic autofix (Coccinelle `make coccicheck`) → docs linter
(`scripts/kernel-doc -Wall`).

Not mechanically checkable (needs review judgment): nesting-depth intent, name quality,
function size, goto-label semantics, comment intent, refcounting design,
action-vs-predicate return conventions, WARN judgment.
