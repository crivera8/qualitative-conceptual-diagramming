# Changelog

Changes to `index.html` (the Figure Creation Agent — Thea & Drew).

## 2026-10-05

Sixteen commits: a voice bug that took four wrong theories to pin down, the
example cards reassigned across the three model types, and some copy.

### The voice bug

Thea was meant to read in a female-sounding voice and Drew in a male one.
Drew kept sounding like Samantha. Four explanations were tried and three of
them were wrong, which is worth recording so they are not tried again:

- *"The sandbox exposes no voice list."* Wrong. A readout added to measure
  it reported **199** voices, and resolved Drew to Daniel [en-GB]
  (`eeab159`). The list is simply populated **late** — 0 voices early, 199
  a moment later in the same frame. `voiceschanged` alone did not surface
  them; a poll did, and it now starts at page load rather than when voice
  mode is switched on, so a first reply no longer speaks with the
  pitch-only fallback exactly once.
- *A `#debug` hash would show the readout* (`81591d5`). It can never fire:
  the artifact iframe's `src` carries a query string and no fragment, so no
  hash ever reaches the page. Replaced with a key chord.
- *Ctrl+Alt+D as `e.key === 'd'`* (`5c8fe8f`). On macOS, Option rewrites
  `e.key` to `'∂'`. Matched on `e.code === 'KeyD'` instead, and added
  Shift-click on the Voice button, since a chord also needs focus inside
  the iframe.

The actual cause was three separate things, all present, all fixed together
in `d1fc4e5` rather than guessing which one mattered:

1. **A stale voice object.** The chosen `SpeechSynthesisVoice` was cached
   as an object from an earlier `getVoices()` call. When the engine
   repopulates its list, a handle from the previous list is silently
   ignored. The cache now holds the voice **name** and re-resolves the live
   object immediately before each utterance.
2. **An unconditional `cancel()`.** `speak()` called
   `speechSynthesis.cancel()` before every utterance, including when
   nothing was speaking — which makes some engines drop the voice on the
   new utterance and fall back to the default. It now cancels only when
   speaking or pending.
3. **`lang` set alongside `voice`.** Setting `utterance.lang` next to
   `utterance.voice` makes some engines re-select by locale and discard the
   assigned voice. The voice already carries its locale, so `lang` is set
   only in the no-voice fallback, where it is the only steer available.

Pitch was also pushed apart so the two stay distinguishable where only one
voice is installed: Drew's fallback to 0.1 (`02fa367`, `17aeef3`) and
Thea's raised (`0dba26d`).

### Example cards reassigned

- **Two model-type options reworded** (`cc83559`). Process-based is now
  "things unfold or lead to each other, without a focus on variance across
  units of analysis"; Sequential is "a series of stages that unfolds across
  time, though the exact steps may differ slightly across actors".
- **The examples moved** (`cc83559`). Sayegh went from the process card to
  the **variance** card, bringing its embedded Figure 1 with it — still
  permitted, since CC BY does not care which card it sits on. Lee &
  Young-Hyman (2026) became the process example. **Sequential lost its chip
  entirely:** there is no published example for it, and Tilcsik is not open
  access. Its entry is kept rather than deleted, because the schematic and
  claim line are still wanted; only the missing example is the reason the
  card is not shown.
- **One note per figure, not two** (`1956ea3`). The variance card described
  the same figure twice — a caption under the image and a citation line
  below it. The caption went. The **licence did not**: CC BY requires
  attribution naming the licence, and the caption was the only place it
  appeared, so the citation line now carries it. `figureBlockHTML` renders
  a `figcaption` only when there is credit text for it, so a future figure
  that needs its own caption still gets one.
- **Lee & Young-Hyman's actual Figure 1 embedded** (`aa5c02e`), replacing
  "cited and linked only". See the licence section below — this one is not
  on the same footing as Sayegh's.

### Intake

- **A unit-of-analysis field** on the intake screen: "What is the unit of
  analysis?", with "e.g., individual, group, organization" as the hint, and
  a plain text box. It sits between "How do those things relate to each
  other?" and "What sort of model do you think this is?".

  In the summary sent to Thea it is placed immediately **before** the model
  line rather than appended with the other optional fields, because the
  Process-based option is defined as "without a focus on variance across
  units of analysis" — Thea has to read the two together to say anything
  useful about which model type fits. It also appears in the one-line
  receipt in the feed. Optional, like the other free-text fields: when it is
  left blank both the summary line and the receipt segment drop out
  entirely.

### Copy

- **A sign-in notice on the consent screen** (`4ee0d42`), cut to a single
  sentence (`ba5798d`): "You'll need to be signed in to Claude to use this
  tool." It sits before "Where your words go".
- **The intake lede's opening sentence** (`5a1c8d9`) now reads "This AI tool
  is designed to help management researchers with qualitative data develop
  figures for their research."

### Licences — a fourth state, and the first conditional one

The licence notes beside `MODEL_EXAMPLES` now record four papers in four
states, including the two no longer displayed. The one that changes how
this repo has to be handled from here on:

**Lee & Young-Hyman is CC BY-NC 4.0**, and its Figure 1 is embedded. That
is not the same permission as Sayegh's CC BY. NonCommercial makes the embed
**conditional on how this tool is distributed** — it is permitted because
the tool is given away free as a research companion. If it is ever sold,
bundled into something sold, or used to promote a commercial offering, that
figure has to come out; Sayegh's would not. Nothing in the page enforces
this, so it travels in the licence comment and in `README.md`.

Two things that make this easy to get wrong. The licence is **not printed
in the article PDF at all** — page 1 carries only SAGE's standard "Article
reuse guidelines" line, which on its own reads as all-rights-reserved — so
the permission rests on the publisher's own CrossRef deposit for the version
of record, checked 2026-10-05. And the figure's two upside-down "supports"
labels are set that way in the published original: the page is printed
rotated 90° and is rotated back as a whole, so every label keeps its
original orientation.

On encoding it: 300dpi from page 32, kept at its native 2531×1621 and
reduced to a 128-colour palette, 180KB. **Downscaling first made the file
larger** — 1800px wide came out at 228KB — because resampling smears
antialiasing across flat line art that otherwise compresses cleanly. Worth
remembering before resizing the next one.

---

## 2026-10-02

Sixteen commits. Grouped by what they touch rather than by commit order.

### Bugs

- **The intake screen's top was unreachable** (`33f3bb3`). Ticking the consent box and pressing Continue appeared to do nothing. The screen *was* shown — scrolled into its own middle with no way back up. `.screen` used `display:flex` with `align-items:center`, and centring a flex item taller than its container overflows in **both** directions; the overflow past the start edge cannot be scrolled to, because `scrollTop` cannot go negative. Measured at 440×620: the card is 1,273px tall and its top sat at −326px with `scrollTop` already 0. It overflowed at 878×833 too, so this affected ordinary desktop windows, not just narrow panels. The consent screen escaped only because `.consent-scroll` caps itself at `44vh`. Fixed with `align-items:flex-start` plus `margin:auto` on `.screen-card`, which centres when it fits and collapses to 0 when it does not. **This bug predates all of today's work.**

- **A moderator could not be drawn on a relationship** (`695828c`). `add_arrow` rejected any endpoint that was not a shape, and both renderers resolved endpoints against shapes only — so an arrow landing on another *arrow*, which is the shape that distinguishes a variance model, was inexpressible and would have rendered as nothing. Added `resolveAnchor`/`arrowPoints` (recursive, depth-limited at 4 so a loop terminates), accepted arrow ids in `add_arrow` and `update_arrow`, made `delete_element` drop arrows attached to a deleted arrow, marked them in `buildStateSummary`, and taught `auto_layout` to hold moderators out of the column flow and place them above the relationship they modify. `computeEdgePoints` was superseded and removed.

- **pdf.js's "Setting up fake worker" warning** (`6bede59`). Now suppressed via `verbosity: ERRORS` on `getDocument`. Verified against the real `pdf.min.mjs` 6.3.289 rather than assumed: `warn()` gates on a module-global level and `setVerbosityLevel` is not exported — but `getDocument` calls it itself, at byte offset 251,225, before `PDFWorker.create` at 251,439, so the option reaches the check in time.

- **Rate-limit copy stated a wait it could not know** (`823b3b1`). `sample` errors carry only `{code, message}` — no `retryAfter` — so `rateLimitCopy` now shows a duration only when the service's own message states one, requiring both a retry cue and an `in/after/within` lead-in in the same sentence. Looser matching read "50 requests per minute" as "try again in 50 minutes", which is the same confidently-wrong-number failure being fixed.

### Features

- **Distinct voices** (`2bf85c3`). Thea reads in a female-sounding voice, Drew a male-sounding one. `speak()` previously took only the text, so both used the browser default; `pushAssistant` already knew the persona and was dropping it. The Web Speech API exposes no gender attribute, so selection matches the voice names platforms actually ship and caches the result; pitch (1.15 / 0.8) applies regardless, so the two stay distinguishable where only one voice is installed. A `voiceschanged` listener re-resolves the cache, since `getVoices()` is empty until the engine loads.

- **An introduction on the intake screen** (`0337d08`). It previously opened straight into Thea's greeting with nothing saying what the tool was for. Reuses the consent screen's kicker and title classes so the two gate screens read as a pair.

- **Intake layout tightened, 1,227px → 1,097px** (`ea7c12a`). Spacing only. The largest single win was `.mic-status`: a `<p>`'s default margins were reserving 21px around a status line that is empty until dictation is used.

- **Variance-based redefined** (`dcdfa07`) to "constructs and the relationships between them explain variation in an outcome; the arrows carry the argument, not a time order". Thea's own copy of the definition was updated to match, and her lumped "process-based or sequential" paragraph split so each has its own guidance.

- **"See an example" cards** (`5e6ccca`) on all three model-type options: a schematic, a one-line statement of what that kind of figure claims, and a cited published example. Content lives in `MODEL_EXAMPLES`, away from the layout code, with a per-example `visual.kind` because the three examples sit in three different licence positions. The chip is a real `<button>` with `aria-expanded`/`aria-controls`; pinning is the primary interaction and hover the enhancement, since touch has no hover and pinning is what makes the card keyboard-reachable; Escape closes and returns focus; one card open at a time. The chip sits **outside** the `<label>` — inside it, opening an example would have selected that model type.

- **The Process-based and Sequential descriptions were briefly rewritten, then restored** (`00295c5`). They had been changed because they contradicted the schematics the cards were built to show: Process-based read "one shared sequence … in roughly the same way for everyone" while its drawing branches to different outcomes, and Sequential read "the exact steps may differ slightly across actors" while its drawing is a single unbranching chain — effectively swapped. Restored to the original wording at the authors' direction, with the schematics kept as drawn. **So the process card's description and its drawing disagree, deliberately.** A branching model is still a process model, and that is the misreading the card exists to correct.

- **Sayegh's actual Figure 1** (`a061e7b`, re-encoded `0c77270`). Shown beneath the schematic on the process card, clickable to full size. Permitted because Sayegh is confirmed CC BY 4.0. Embedded as a `data:` URI because the artifact CSP blocks external images silently, and opened in an in-page overlay rather than a link because the sandbox will not open a `data:` URI as a top-level document. Re-encoded from 780×395 to **2000×896**, cropped from page 13 of the article PDF at 300dpi and reduced to a 128-colour palette (180KB, against 494KB full-colour for no visible gain on line art). At the old size the pathway labels were unreadable even in the overlay, which defeated the point of showing the real figure.

- **Figure-draft upload** (`4faff08`), ported from a diverged copy and reworded. A tester asked whether "Have an existing draft?" wanted a draft of their journal article; it now reads "Already have a draft of the figure?" and says "It's the figure I need, not the whole paper." jszip and pdf.js are lazy-loaded only once a file is attached; extracted text is capped at 3,000 characters and appended to Thea's intake summary.

### Merged from a diverged copy (`cb1a87f`)

A second copy of this tool existed outside the repo, still calling `api.anthropic.com` directly — so it could not run as a published artifact, and the merge ran in this direction. Its four unique features:

1. **A new intake field**, "How do those things relate to each other?", with dictation, between the factors and the model type. It collects exactly what Thea exists to interrogate.
2. **Longer, more argumentative claim lines** on the example cards, chosen over the shorter ones written here.
3. **Curved arrows with sibling separation.** Two arrows between the same pair of boxes previously landed on exactly the same line and hid each other — and Drew is instructed to draw a feedback loop as two opposing arrows, so this was the common case. Measured: a feedback pair's labels were 0 units apart and are now 24. A lone arrow still renders as a straight `<line>`.
4. **A separate `layer-arrow-labels`**, drawn after the shapes, so a label cannot end up beneath a line or box painted later. `renderArrows` split into `renderArrowLines` and `renderArrowLabels`.

Kept from this build rather than the fork's: the label machinery. The fork's `renderArrowLabels` has no balanced wrap, no rotation and no collision search, and adopting it would have undone both arrow-label fixes recorded below. A curved arrow anchors its label on the curve's own midpoint; a straight one still runs the collision search.

PPTX separates siblings too, as parallel straight lines — a PowerPoint line shape cannot hold the quadratic curve.

### Consent screen

- **Model-training opt-out instructions** (`f977cd7`). Points to Settings → Privacy → "Help Improve our AI models" in Claude.ai or the mobile app, notes that opting out stops future training use but does not remove data already used, and links Anthropic's own article at `privacy.claude.com`.

### Licences

- **Shen confirmed** (`0c77270`). Previously recorded as unconfirmed. The article PDF's own copyright statement reads "open-access article distributed [under the Creative] Commons Attribution License (CC BY)", checked 2026-10-02. Its Figure 1 may therefore be reproduced with attribution; it is still not embedded, which is a content decision rather than a licence one. Tilcsik remains not open access.

### Documentation

- **This changelog** (`bfb87f1`), created to preserve the two arrow-label fixes below.
- **`README.md`** added, including the constraint that matters most: this tool cannot run as a plain web page, because `claude.use` exists only inside an artifact runtime.

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
