# Figure Creation Agent — Thea & Drew

A thinking-and-drawing partner for building conceptual figures out of qualitative research. Two personas share one conversation and one diagram:

- **Thea** does the conceptual work — what the constructs actually are, what job each arrow is doing, whether the relationships are specific enough to draw. She states her call and holds it unless given genuinely new information.
- **Drew** turns settled decisions into boxes and arrows, and exports them as SVG, PNG or PowerPoint. There is no dragging: you tell him what to change and he redraws.

See `CHANGELOG.md` for what has changed and why.

## Read this before you host it anywhere

**This tool only runs inside a Claude artifact.** Both personas reach Claude through `claude.use('sample')`, and the export buttons hand files to the viewer through `claude.use('downloads')`. Neither exists on a plain web host or in a file opened from disk.

| Where it's hosted | Intake & figure editing | Thea & Drew replying | SVG / PNG / PPTX export |
|---|---|---|---|
| Published Claude artifact | Works | Works | Works |
| GitHub Pages or any static host | Renders | **Fails** | **Fails** |
| Opened as a local file | Renders | **Fails** | **Fails** |

There is no API key to add and no proxy that fixes this — unlike the Casting Call tool, there is no `api.anthropic.com` call to route anywhere. `window.claude` is supplied by the artifact runtime or it is absent. When absent, the chat says so rather than failing silently:

> Thea and Drew talk to Claude through this page's own Claude access, which isn't available right now — try reloading this artifact.

So: host the repo for the source, publish the artifact for the tool.

## What "the code" is

`index.html` is the whole thing — one file, ~3,250 lines, with CSS and JavaScript embedded. No build step, no framework, no package manager.

Three libraries are lazy-loaded from CDNs, and only when first needed. All three are on hosts the artifact CSP allows for scripts:

| Library | Host | Loaded when |
|---|---|---|
| pptxgenjs | jsDelivr | first PPTX export (`loadPptxLib`) |
| jszip | cdnjs | a `.pptx` is attached |
| pdf.js | cdnjs | a `.pdf` is attached |

Each failure is reported to the reader rather than swallowed.

## Publishing it

The published artifact is this file with its document wrapper removed — the artifact host supplies its own `<!doctype>`, `<head>` and `<body>`:

1. Delete the `<!DOCTYPE html>` / `<html>` / `<head>` block, keeping the `<title>` and `<style>`.
2. Delete the closing `</body></html>`.
3. Publish with `capabilities: {sample: {}, downloads: true}` — without the declaration both capabilities resolve `null` and the tool degrades to read-only.

## Landmarks

**Asking Claude.** `callClaude` is the single entry point for both personas; the persona only changes which system prompt is sent and how the reply is parsed. `sampleErrorCopy` maps the capability's error codes to what a researcher should be told, and `rateLimitCopy` shows a wait only when the service's own message states one.

**The personas.** `THEA_SYSTEM` and `DREW_SYSTEM_BASE`. Drew's is appended with `buildStateSummary()` on every turn, so he always sees the current shapes and arrows with their real ids.

**Drew's tools.** He replies with one JSON object rather than using API tool-calling; `parseDrewResponse` reads it (with a fallback for the XML tool-call syntax he occasionally reverts to), and `runDrewActions` → `executeDrewTool` applies it. `tempId` lets him reference a shape or arrow he is creating in the same response.

**Geometry.** `arrowPoints`/`resolveAnchor` resolve an arrow's endpoints, which may name a shape **or another arrow** — that is how a moderator attaches to a relationship. `computeArrowOffsets` and `arrowCurveGeometry` separate sibling arrows between the same pair of boxes. `pickLabelAnchor` and `wrapLabelBalanced` place and wrap arrow labels.

**Rendering.** `renderToStage` draws four passes in order: groups, `renderArrowLines`, shapes, `renderArrowLabels`. The label pass is last and lives in its own layer so a label can never end up under a line or box. `buildExportSVG` and `buildAndSavePptx` must stay consistent with each other — a change to one that is not made in the other shows on screen and vanishes from the exported file.

**Example cards.** `MODEL_EXAMPLES` holds all the content for the three "see an example" popovers, away from the layout code. Read the licence comment beside it before touching the figures.

**Voices.** `VOICE_PREFS` and `pickVoice`. The Web Speech API exposes no gender attribute, so selection matches platform voice names and falls back to pitch.

**Figure upload.** `wireFigureUpload`, `extractTextFromPdf`, `extractTextFromPptx`.

## Figure licences — three different positions

The schematics on the example cards are our own drawings, because re-hosting a published figure depends on that figure's licence even when the paper is open access. The three examples are **not** in the same position, and must not be collapsed into one statement:

| Example | Status | What's allowed |
|---|---|---|
| Sayegh (process) | **Confirmed CC BY 4.0** | Reproduced in the card, with attribution. |
| Shen (variance) | **Confirmed CC BY** — checked 2026-10-02 against the article PDF's own copyright statement | Could be reproduced with attribution. Deliberately not embedded; that is a content decision. |
| Tilcsik (sequential) | **Not open access** (AMJ, 2010) | Cite and link only. Never reproduce. |

`visual.kind` is per-example for exactly this reason. `renderExampleVisual` already handles `kind: 'reproduction'`, so swapping a real figure in is a data change here, not a refactor.

## Editing notes

**Ask for targeted edits, not rewrites.** With a single file this size, "rewrite it with X changed" invites silent regressions in parts nobody was looking at.

**Check the diff before committing.** One caveat: the embedded figure is a ~240,000-character base64 string on one line, so `git diff` on `index.html` can look alarming when that line moves. `git diff --stat` first.

**Keep the two export formats in step.** The PPTX path has repeatedly been the one that silently lost a feature the SVG path gained.

**Re-encoding the embedded figure.** The source PNG is at `figures/sayegh-2025-figure1.png`, cropped from page 13 of the article PDF at 300dpi and reduced to a 128-colour palette (2000×896, 180KB — a full-colour PNG of the same crop was 494KB for no visible gain on line art).

## Known rough edges

- **Not verified end-to-end:** the curved-arrow separation and its PPTX export, and the PDF/PPTX text extraction. All three need the live artifact; their geometry and wiring are tested, their behaviour in the published page is not.
- **pdf.js worker.** pdf.js loads its worker from cdnjs, which the script allowlist does not cover for workers. pdf.js has its own workaround (`_createCDNWrapper`), so whether the worker loads in a published artifact is unmeasured. Either way extraction works — off-thread if it loads, on the main thread if not — and the warning it would log in the fallback case is suppressed at the `getDocument` call.
- **A diverged copy of this tool existed** outside the repo, with four features this build lacked. They were merged in on 2026-10-02 and that copy retired. If another one appears, diff it before assuming it is older.
- **Rate limits.** `sample` errors carry no `retryAfter`, so no countdown is possible; a duration is shown only when the service's message states one.

## Contact

- Vanessa Conzon, Boston College — conzon@bc.edu
- Allie Feldberg, Harvard Business School — afeldberg@hbs.edu
