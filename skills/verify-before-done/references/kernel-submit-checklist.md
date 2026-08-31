# Kernel pre-submission checklist — full extraction

Source: `Documentation/process/submit-checklist.rst` + `5.Posting.rst`. The maximal
worked example of gate-based verification. Translate categories, not commands.

## Review your code
1. `#include` what you use — never rely on indirect includes.
2. Style per coding-style doc.
3. Every memory barrier carries a comment explaining what and why.

## Config-system changes
- New options default to off; all options have help text; reviewed against relevant
  option combinations.

## Documentation (same change, not follow-up)
- kernel-doc for global APIs; `/proc` entries, boot parameters, module parameters,
  userspace interfaces (`Documentation/ABI/`), ioctl numbers all documented where added.

## Tool gates
- `scripts/checkpatch.pl` (with judgment); clean under sparse; `make checkstack` findings fixed.

## Build matrix
- Clean with affected options `=y`, `=m`, `=n`; no compiler/linker warnings.
- Passes `allnoconfig` and `allmodconfig` (everything-off and everything-on builds).
- Builds out-of-tree (`O=builddir`); docs build without new warnings.
- Multiple CPU architectures (cross-compile); new code under `gcc -W`.
- Option-combination sweeps (SMP on/off × debug features etc.).

## Runtime test matrix
- All heavyweight debug instrumentation **simultaneously enabled** (lockdep, slab debug,
  page-alloc debug, atomic-sleep checks, RCU proving…). Modern parallel: run tests under
  sanitizers/assertions builds, not release builds.
- With and without SMP/preemption (concurrency configs both ways).
- All codepaths exercised with lock validation on.
- **Injection of allocation failures** — error paths must actually execute.
- Tested against the latest integration tree (`linux-next`) — the freshest base, so
  your change coexists with everything else queued.

## Pre-post extras (5.Posting)
- Tests added in the existing framework (KUnit).
- Benchmarks run when there are performance implications; claims quantified.
- Legal right to post confirmed (employer sign-off where applicable).
