# Changelog

Changes to `index.html` (the Figure Creation Agent — Thea & Drew).

## 2026-10-01

### Consent screen

- **Added model-training opt-out instructions.** Points to Settings → Privacy → "Help Improve our AI models" in Claude.ai or the mobile app, notes that opting out stops future training use but does not remove data already used, and links Anthropic's own article at `privacy.claude.com`.

---

## Recovered history — 2026-09-24

The two arrow-label fixes below were developed in a separate working repository that was shared as a git bundle rather than pushed here. That repository did not share a commit root with this one, so its history could not be merged in; the commit messages are preserved verbatim below instead, because they carry the root-cause analysis for two non-obvious geometry bugs.

Both are authored `Claude <assistant@example.com>`. The code from both is present in `index.html`. The bundle itself has been discarded now that this record exists.

Note on the diffstat for the first commit: it reports the whole file as an insertion because it was the root commit of that fresh working repository, not because the change itself was that large. The message describes the actual change.

### `b67b2e6` — Fix label overlapping its own endpoint box (rotation swing)

*2026-09-24 16:16:07 +0000 · `index.html` | 14 insertions, 1 deletion*

> 'produces (external)' on the Centering->Opacity arrow was genuinely
> overlapping 'Centering quantification' -- its own source box -- not a
> centering issue. Computed the exact rotated bounding box: one corner
> lands ~20 units inside that box, confirming a real geometric overlap.
>
> Root cause: pickLabelAnchor explicitly excluded an arrow's own two
> endpoint shapes from its collision check, on the assumption that
> checking them would always trigger a false rejection right at the
> connection point. That assumption was wrong here -- a rotated label's
> corners can swing well beyond its own half-height (proportional to
> width * sin(angle)), overlapping a nearby shape including, in this
> case, its own endpoint, and excluding endpoints meant this was never
> even attempted to be avoided.
>
> - Removed the fromId/toId exclusion; only group containers (unfilled
>   outlines) are still excluded
> - Verified with no regression: tested against both this new case and
>   the two cases fixed in earlier rounds -- the new case now resolves
>   (no overlap), and both prior cases pick the identical anchor point
>   as before (a normal label already clears its own endpoints, so
>   checking them changes nothing there; the difference only shows up
>   in genuine overlap cases, where it now actually avoids them)
>
> Verified against the user's real coordinates (parsed from their
> uploaded file) in both the real SVG renderer and real PPTX/LibreOffice:
> full label text visible, clear of the box, in both.

### `f471c0e` — Balance multi-line arrow label wrapping so centering reads clearly

*2026-09-24 16:04:47 +0000*

> Every wrapped line already shared the same anchor point via
> text-anchor:middle -- technically centered from the start. The actual
> problem was the wrap itself: greedy-fill packed each line as full as
> possible before breaking, which for a label like 'keeps construction
> visible externally' produced 'keeps construction visible' / 'externally'
> -- three words then one orphaned word. Still centered, but a big width
> mismatch between stacked lines doesn't read as centered to the eye.
>
> - Added wrapLabelBalanced(): binary-searches for the narrowest width
>   that still produces the same line count as the greedy wrap, which
>   redistributes words evenly across those lines instead of front-loading
>   them. For the reported label this changes the split to 'keeps
>   construction' / 'visible externally' -- near-identical widths (94.7 vs
>   85.0), matching what the user's actual image showed
> - Applied to both SVG and PPTX arrow labels uniformly
>
> Verified visually in both the real SVG renderer and real PPTX/
> LibreOffice: every multi-line label now reads as clearly centered.
>
> Note: container reset since the last session, so this continues as a
> fresh repo rather than the previous history -- happy to reconcile if
> the user shares back their existing clone.

### Where that code lives

Both fixes are in the arrow-rendering section of `index.html`:

- `wrapLabelBalanced()` — the binary-search balanced wrap, called by both `renderArrows()` (SVG) and `buildAndSavePptx()` (PPTX) so the two formats wrap identically.
- `pickLabelAnchor()` — the collision search. Its comment block records the endpoint-exclusion reasoning; group containers are still skipped there because they are unfilled dashed outlines and a label inside one hides nothing.
