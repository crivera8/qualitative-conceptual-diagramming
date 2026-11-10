# Thea–Drew Testing Summary for Editors

Oct 19, 2026 · @Madushani (Madu) Rajapaksha WasalaMudiyanselage

## Scope of this summary

This draws from engineering verification done while building and fixing the Figure Creation Agent tool across the development conversation — not a dedicated testing-team protocol with planned sessions or scripted prompts. Of the editors' six items, this material can speak to:

- **#3** (failure modes and changes) — fully, in detail
- **#4** (model/version) — as an architectural fact, not a per-test version number
- **#5** (data handling) — verified directly against the tool's code
- **#6** (screenshots) — partially: rendered-figure images exist, not a full walkthrough

It can't speak to **#1** (a testing team's session counts and paths) or **#2** (redirection tests with verbatim excerpts) — those describe a kind of testing (people deliberately probing Thea's and Drew's persona behavior and logging the exchanges) that didn't happen as part of this work.

## What was tested, by area

Verification happened per fix, not as a fixed test matrix, so there's no clean "session count" — each round (see **Failure modes**, below) was checked against the specific bug it targeted, using a mix of:

- **Real inputs**: Vanessa M. Conzon and James C. Mellody's *Concerted Quantification* paper (Organization Science, 2025) was used as the actual research content run through Thea's intake and Drew's diagram generation — not a synthetic placeholder.
- **Real libraries, not just code review**: PPTX output was checked by generating actual files with the real pptxgenjs library and rendering them through LibreOffice, not just reading the generation code.
- **Cross-format consistency checks**: SVG, PNG, and PPTX outputs were compared directly against each other for the same diagram — legend symbols, dash patterns, arrow rotation, label placement.
- **Areas covered**: the diagram renderer (SVG/PNG), the PPTX exporter, the save/download mechanism, Drew's JSON-action parser, and the live connection to Claude for both Thea's and Drew's turns.

## Failure modes found and what changed

19 fixes across the development conversation, newest first. Exact per-fix calendar dates aren't reliably available — two container resets during the work lost the git history that would have carried them — so this is ordered, not dated; all of it falls within the same development conversation.

| Found | Changed |
| --- | --- |
| A rotated label's corner could swing into its own source/target box | Collision search now checks an arrow's own endpoints too, not just other shapes |
| A very uneven line-wrap (three words, then one orphaned word) made centered text look off-center | Wrapping now balances line widths instead of just greedy-filling |
| Group containers (dashed, unfilled outlines) were treated as solid obstacles, pushing labels into real boxes instead | Group containers no longer count as collision obstacles |
| A label could sit right where its own text overlapped a nearby box, with no way to reposition it | Added a search that tries several points along the arrow and picks whichever is clear of every other shape |
| Near-vertical arrows kept their labels upright while every other angle rotated — an inconsistency | Every arrow label now rotates to match its line, no exceptions |
| The live chat view showed two legends at once (diagram preview + exported file both baked one in) | The on-screen preview now omits the legend it already shows separately |
| A label could still be wider than a short arrow, spilling into neighboring boxes | Wrap width and label offset now scale with how short the arrow actually is; wrapping also breaks at `/` and `-` inside long words |
| PPTX text overflowed its boxes on wide diagrams; labels sat centered on top of lines instead of beside them | Font size now scales with box size the same way SVG does; labels moved to sit offset above the line |
| A stray sentence before Drew's JSON (against his own instructions) caused the whole turn to silently draw nothing | Parser now recovers the JSON even with a prose lead-in |
| PPTX's "feedback" dash style read far coarser than SVG's fine dash-dot pattern | Switched to a tighter dash preset, confirmed by rendering both side by side |
| PPTX's co-occurrence marker was a solid-filled dot; SVG's is hollow (white fill, black outline) — a different shape, not just a color | Drawn as a real hollow ellipse shape in PPTX instead of relying on a built-in line-end type |
| First pass at the co-occurrence fix (round shape, still solid-filled) | Superseded by the hollow-ellipse fix above |
| The legend sat at a fixed "worst case" height in PPTX, leaving a large gap under any diagram that didn't need all of it | Legend now anchors to the diagram's actual height |
| pptxgenjs was loaded from a cdnjs URL that never existed — the library has never been hosted there | Pointed the loader at the correct jsDelivr URL; confirmed the library actually loads |
| Every export error went through `alert()`, which a published page doesn't reliably show — PPTX could fail with nothing visible | Errors now show on a status line on the diagram card instead |
| A plain file-save click only works in the chat's own preview — it's inert once a page is published | Routed SVG/PNG/PPTX saves through the platform's `downloads` capability |
| No legend in exported SVG/PNG at all; PPTX library loaded once with no retry on failure | Legend now baked into exports; PPTX library loads on demand with retry |
| Direct calls to `api.anthropic.com` only work in the chat's own preview — blocked once the tool is actually published | Replaced with the platform's `sample` capability so Thea and Drew work once shared |
| Original tool as authored | Starting point: Thea (thinking partner) and Drew (drawing partner), built as a working example of the "Casting Call" persona framework |

## Model and version

The tool calls Claude through the platform's `sample` capability with `modelTier: "default"` — not a pinned model string. That's been true since the very first working version and was never changed afterward.

This means there's no single model version to report per test. Whatever model actually sits behind "default" is decided by the platform, not by this file, and isn't visible from the tool's own code — it may have changed between earlier and later rounds of testing with no corresponding code change on our end.

## Data handling

Checked directly against the current code (not from memory):

- **What leaves the browser**: conversation turns (intake answers, messages to Thea and to Drew) are sent to Claude via the platform's `sample` capability each time a turn is sent. When exporting a figure, the generated SVG/PNG/PPTX file is handed to the platform's `downloads` capability so the viewer can save it to their own device.
- **What's fetched in**: only the PPTX-export code library, loaded from jsDelivr's CDN the first time someone clicks the PPTX button — a static script, not user data.
- **What's stored**: nothing, by this tool's own code. Searching the full source turns up zero uses of `localStorage`, `sessionStorage`, `indexedDB`, or the platform's own persistent-storage capability — confirmed directly, not inferred. The diagram and conversation live only in the browser tab's memory for that session and disappear on reload.
- **One limit on this confirmation**: this covers what the tool's own code does. It can't speak to the platform's own infrastructure-level logging (standard API request logging Anthropic may do, for example, for abuse monitoring) — that's governed by Anthropic's own data-handling policies, not visible from this file.

## Screenshots

What exists: several rendered-figure images from this work, across SVG, PNG, and PPTX, captured at various points to verify fixes (legend placement, label rotation, arrow types). These show the output figure, not a full conversation.

What doesn't exist yet: a screenshot walkthrough of a complete Thea–Drew session — intake through conversation through the final rendered figure, all in one capture. That matches what the editors already anticipated by suggesting Elizabeth S. capture this fresh.

## Not included here

Two of the editors' six items aren't derivable from this material at all, flagged rather than guessed at:

- **#1**: a testing team's list of paths/screens/inputs/session counts — this work wasn't organized as a set of discrete tester sessions.
- **#2**: redirection tests with verbatim excerpts (pushing Thea off the analytical role, or Drew into it) — no one ran that kind of deliberate adversarial probing against the personas during this work and logged the exchanges.

Both would need a separate, dedicated testing pass against the live tool.
