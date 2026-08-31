# Kernel AI-contribution policy — full extraction

Source: `Documentation/process/coding-assistants.rst`, `generated-content.rst`.

## coding-assistants.rst

- AI tools must follow the standard process docs (`development-process`, `coding-style`,
  `submitting-patches`); generated content governed by `generated-content.rst`.
- Licensing: GPL-2.0-only compatible, proper SPDX identifiers.
- **"AI agents MUST NOT add Signed-off-by tags. Only humans can legally certify the DCO."**
  The human submitter reviews all AI-generated code, ensures license compliance, adds
  their own sign-off, and takes full responsibility.
- Attribution: `Assisted-by: LLM [TOOL1] [TOOL2]` — optional tools are specialized
  *analysis* tools (coccinelle, sparse, smatch, clang-tidy); basic dev tools (git, gcc,
  make, editors) must not be listed. Example: `Assisted-by: LLM coccinelle sparse`.

### Mandatory 9-step bug-find-and-fix procedure
1. Read the whole process documentation, not keyword-searched fragments.
2. Note the commit ID; locate the bug as instructed.
3. Non-trivial bug → attempt a reproducer; unverified reports are often invalid and get
   ignored. Stop if it looks wrong.
4. Write a fix — "this part is not optional"; same-session fixes are more accurate.
5. Build and verify; drop non-working fixes; no new build warnings; passes checkpatch.
6. Commit with problem/solution message + `Fixes:`; no Signed-off-by; add `Assisted-by:`.
7. Identify maintainers/lists via `scripts/get_maintainer.pl`.
8. Explicitly state what could not be done (build/test/reproduce).
9. Classify bug vs vulnerability per `threat-model.rst`; route accordingly;
   **"the assistant must never send anything itself"** — leave the result to the human.

## generated-content.rst

- Premises: tools increase contribution volume; reviewer/maintainer bandwidth is scarce;
  knowing human-vs-tool provenance preserves trust.
- **Out of scope** (no disclosure required): spelling/grammar fixes, completion/boilerplate,
  mechanical renames, reformatting (Lindent/clang-format/rustfmt) — though telling the
  reviewer which tool was used may still help.
- **In scope**: any meaningful content not written by a person in the sign-off chain —
  tool-suggested fixes (`checkpatch.pl --fix`), Coccinelle scripts, chatbot-written
  functions, AI-generated or translated changelogs, tool-found problems (note the finding
  tool in the changelog). **"If in doubt, choose transparency."**
- Guidelines: read and understand the DCO first; "ensure that you understand your entire
  submission and are prepared to respond to review comments."
- Disclose: tools used, tool inputs (e.g. the script), prompts (verbatim if short,
  summarized if long), affected portions, and testing performed.
- Maintainer discretion: treat normally, reject outright, apply extra testing/scrutiny/
  lower priority, ask about model training, require the submitter to demonstrate
  understanding, or suggest a better prompt instead of code changes.
- Accountability: scrutiny scales with generated fraction; "you are expected to
  understand and to be able to defend everything you submit. If you are unable to do so,
  then do not submit"; violators' series may be rejected without detailed review.

## Reconciliation notes
- `Assisted-by:` trailer (coding-assistants) and narrative disclosure
  (generated-content) are complementary: compliant practice is both.
- The tag's basic-tools exclusion applies to the trailer only; changelog disclosure still
  covers checkpatch/Coccinelle-class tools.
- Agent may author commits and trailers, but submission — and sign-off — are human acts.
