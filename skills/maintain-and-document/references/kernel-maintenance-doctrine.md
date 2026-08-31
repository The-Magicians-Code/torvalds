# Kernel maintenance doctrine — full extraction

Source: `4.Coding.rst`, `stable-api-nonsense.rst`, `management-style.rst`,
`maintainer-handbooks.rst`, `kernel-docs.rst`.

## Regressions (4.Coding)
- "With few exceptions, changes which cause regressions will be backed out if the
  regression cannot be fixed in a timely manner."
- Linus (2007): "So we don't fix bugs by introducing new problems. That way lies madness,
  and nobody ever knows if you actually make any real progress at all."
- Worst class: userspace ABI breaks — exported interfaces are supported indefinitely,
  hence require thought, docs, and wide review before shipping.

## No stable internal API (stable-api-nonsense, GKH)
- Deliberate: no stable in-kernel source or binary interface; the user-facing syscall
  boundary is the only frozen surface.
- What makes churn safe: "all of the instances of where this interface is used within
  the kernel are fixed up at the same time" — atomic tree-wide refactor.
- Security fixes often require reworking interfaces so the bug class can't recur; a
  frozen internal API would make that impossible.
- "If there is no one using a current interface, it is deleted" — keeps the kernel
  small and surviving interfaces tested: "unused interfaces are pretty much impossible
  to test for validity."
- "You want a stable running driver, and you get that only if your driver is in the
  main kernel tree" — code that lives with its dependencies gets fixed by whoever
  changes them.

## Abstraction & duplication (4.Coding)
- "Excessive or premature abstraction can be just as harmful as premature optimization."
- Always-zero parameters are probably broken from disuse; maintainers actively remove
  unused arguments. Portability shim layers are "especially frowned upon."
- Copying significant code between subsystems = extract a shared library.
- Heavy preprocessor/conditional use signals cleanup needed; prefer constructs the
  compiler can still see and check.
- Over-inlining: "space *is* time" — bigger binary, slower run; let the compiler decide.
- Concurrency designed in up front; the mac80211 case sat unmerged a year because
  locking had to be retrofitted.

## Tooling doctrine (4.Coding)
- "Any problem caught by the computer is a problem which will not afflict a user later on."
- Layered: compiler warnings (expanded beyond defaults) → debug/assert builds →
  lock-ordering validation → fault injection ("untested code tends to be broken code") →
  static analysis → semantic patching for wide mechanical changes → cross-platform builds.
- Ship the strict debug configuration as a checked-in one-command artifact
  (`make x86_debug.config` pattern).
- Executable docs over prose: `lockdep_assert_held()` beats a comment — "Comments can't
  do that."

## Reversibility (management-style, Linus)
- "The key difference between a big decision and a small one is whether you can fix
  your decision afterwards." Prefer undoable moves; never get cornered.
- Declare uncertainty up front; preliminary decisions are cheap to reverse, guaranteed
  ones are not.
- Technical mistakes are reversible; alienating people is not — hence the people rules.
- "Can't we just do both?" — parallel approaches let the answer emerge.

## Documentation lifecycle (kernel-docs, maintainer-handbooks)
- The index exists because "the same questions … appeared again and again": recurring
  questions are a documentation defect.
- Maintainers "should consider documenting additional requirements … if submissions
  routinely overlook specific submission criteria" — repeated corrections → write docs.
- Uniform per-entry schema (title/author/URL/date/keywords/description); newest-first
  ordering; periodic retirement of obsolete entries; protected "foundational" class.
- In-tree docs are the source of truth over web mirrors; index maintenance goes through
  the normal patch process.
- Changelog is the first documentation of any change; comment volume expectations for
  new code exceed what the legacy tree suggests, but "the code should, itself, be
  readable, with comments explaining the more subtle aspects."
- Always comment: memory barriers, locking rules, major data structures, non-obvious
  dependencies, and anything tempting an incorrect cleanup.
