# Kernel submission rules — full extraction

Source: `submitting-patches.rst`, `5.Posting.rst`, `6.Followthrough.rst`, `maintainer-tip.rst`.

## Subject line
- `[PATCH nn/mm] subsystem: summary phrase`, ≤ ~70–75 chars.
- Prefix from `git log` of the touched paths, never filenames.
- Zero-pad series numbering so sort order = apply order; ≤ ~15 patches per initial posting.
- `v2`, `RFC`, `RESEND` in the bracket; RESEND only for byte-identical resubmission.

## Message structure
```
From: <author>                 (if submitter != author)
<one-line summary>
<changelog, wrapped at 75 cols: context → problem → solution>
<tag block>
---
<ephemeral: per-version changes, diffstat>
<diff>
```
Everything above `---` is permanent; everything below is stripped on apply.

## Changelog content rules
- Convince the reviewer there is a problem worth fixing; state user-visible impact.
- Imperative mood; "changelogs written in abstract words are more precise."
- Numbers for any perf/memory/size claim; oops/compiler output verbatim.
- Self-contained — no forced reading of threads/trackers.
- Prior commits: `commit <12+ char sha> ("subject")`.
- Races/memory-ordering documented as CPU0/CPU1 timing tables.
- Worked rewrite examples (tip tree): "We modify the hot cpu handling…" →
  "Cancel the overflow worker and reschedule it…"; "Let's rename it" → "Rename it".

## Tag taxonomy
| Tag | Rule |
|---|---|
| Signed-off-by | Required; DCO certification; only humans; submitter's last |
| Co-developed-by | Immediately followed by that co-author's Signed-off-by |
| Acked-by / Reviewed-by / Tested-by | Require explicit consent of the named person |
| Reported-by | Bugs only; pair with `Closes:` link |
| Suggested-by / Cc | Implicit permission suffices |
| Fixes | `<12+ sha> ("subject")`; add even when no stable backport needed — machine-extractable provenance |
| Link / Closes | lore/report URLs; `patch.msgid.link` for patch origin |
| Assisted-by | Advanced tooling / AI assistance disclosure |

- Canonical ordering (tip): Fixes → Reported-by → Closes → Originally-by → Suggested-by →
  Co-developed-by+SOB → author SOB → handler SOB → Tested-by → Reviewed-by → Acked-by → Cc → Link.
- No combined tags (`Reported-and-tested-by`) — breaks automated extraction.
- Drop collected Reviewed-by/Tested-by after substantial changes; re-request.
- Handler edits noted as `[ handler: what changed ]` above the tags; preserve `From:` authorship.
- Link-rot policy: archive volatile external references (bugzilla attachment) and cite the archive.

## Series mechanics
- Cover letter `[PATCH 0/n]`: overview, motivation, testing; thread patches as replies.
- Base on current mainline/-rc; `git format-patch --base=auto`.
- Every patch independently builds and runs (bisectability).
- Plain text, inline, no HTML/attachments, no wrapping; UTF-8; `git send-email`;
  test-apply on yourself before posting.
- Recipients: maintainers via `scripts/get_maintainer.pl`, `git log` contributors,
  original reporter, relevant list, stable where eligible.

## Followthrough
- Respond to all comments; "one fatal mistake is to ignore review comments in the hope
  that they will go away. They will not go away."
- Comment-that-changed-no-code → code comment instead (Morton).
- Interleaved replies, trimmed quotes, no top-posting.
- Wait ≥1 week before pinging; expect review within 2–3 weeks.
- Regressions after merge are top priority; unfixed → revert and credibility damage.
- The community assumes future patches from someone who abandoned merged code will be
  unmaintained too — ownership continues after merge.
