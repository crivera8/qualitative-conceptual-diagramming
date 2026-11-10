# Changelog for the updates on September 25, 2026

All notable changes to the Figure Creation Agent (Thea & Drew), in the order they happened.

> **Note on history:** this bundle's own git log only goes back to *Balance multi-line arrow
> label wrapping*. Two earlier container resets lost the local clones that held the fuller
> commit history: everything above that point is reconstructed here from the conversation
> record rather than from `git log`, so it's accurate in substance but the original commit
> hashes for that era no longer exist. Everything from *Balance multi-line arrow label
> wrapping* onward is a real, intact commit in this bundle.

## Fix label overlapping its own endpoint box (rotation swing)
A rotated label's corners can swing well beyond its own half-height — proportional to
`width × sin(angle)` — which let a label overlap its own source/target box in a case the
collision-avoidance search wasn't even allowed to check (it excluded an arrow's own
endpoints on the assumption that would always misfire near the connection point). Removed
that exclusion; verified no regression against every previously-fixed case.

## Balance multi-line arrow label wrapping so centering reads clearly
Every wrapped line already shared one anchor point (genuinely centered), but greedy-fill
wrapping could leave a short, lopsided last line that *looked* off-center. Wrapping now
binary-searches for the narrowest width that doesn't add extra lines, producing more even
line lengths.

## Fix labels clipped by boxes: group containers wrongly treated as solid
The collision search treated group containers (unfilled dashed outlines) as solid
obstacles, pushing a label away from an otherwise-fine position into a real box instead.
Group containers no longer count as obstacles.

## Fix label overlapping a nearby box by picking a clear anchor point
Added `pickLabelAnchor()`: instead of always anchoring a label at an arrow's exact
midpoint, it now tries several points along the arrow's length and picks whichever lands
clear of every other shape in the diagram.

## Rotate every arrow label with no exemptions; fix duplicate legend
Removed a special-case that kept near-vertical arrows' labels upright while every other
angle rotated — one rule for every arrow now. Separately, fixed the live chat view showing
two legends at once (the diagram preview and the exported file both baked one in).

## Fix labels still covering short arrows (all 3 formats)
Taught the line-wrapping logic to also break at `/` and `-` inside an otherwise-unbreakable
word, and made both the wrap width and the label's offset distance responsive to how short
a given arrow actually is.

## Fix PPTX text overflow and arrow labels covering lines (all 3 formats)
PPTX scaled box sizes to fit the diagram but left font sizes fixed, so a wide diagram got
tiny boxes with oversized text. Fonts now scale with the same factor as box dimensions.
Arrow labels also started sitting offset from their line instead of centered on top of it.

## Fix: Drew's prose lead-in before JSON silently drew nothing
Drew's prompt requires pure JSON, but an occasional stray sentence before it broke the
strict parser entirely, silently dropping the whole response with zero actions drawn. The
parser now recovers the JSON even with a prose lead-in.

## Fix feedback dash pattern mismatch between PPTX and SVG/PNG
PPTX's "feedback" arrow style used a long-dash preset that read far coarser than SVG's fine
dash-dot pattern. Switched to a tighter preset, verified by rendering both side by side.

## Fix co-occurs dot: PPTX's line-end 'oval' is always filled solid
OOXML's built-in round line-end always renders filled with the line's own color — there's
no hollow variant. SVG's co-occurs marker is a literal white-filled, black-outlined circle,
a different shape entirely. Now drawn as a real hollow ellipse shape instead.

## Fix co-occurs symbol mismatch between PPTX and SVG/PNG
First pass at the above: gave co-occurrence arrows a round cap in PPTX (closer, but still
solid-filled — refined in the next fix).

## Fix large gap between diagram and legend in PPTX export
The legend was pinned at a fixed "worst-case" reserved height rather than the diagram's
actual scaled height, leaving a large gap for any diagram that didn't need the full
reserved space. Legend now anchors to the diagram's real height.

## Fix PPTX load: pptxgenjs was never on cdnjs, only jsDelivr
The original script URL pointed at a cdnjs path that never existed — pptxgenjs has never
been distributed there. Pointed the loader at the correct jsDelivr URL and verified the
library actually loads and runs.

## Fix silent PPTX failures; add legend to PPTX export
Every export error went through `window.alert()`, which a published artifact's host doesn't
reliably surface — PPTX could fail with nothing visible at all. Errors now report through a
status line on the diagram card. Also added the SVG/PNG legend's equivalent to PPTX.

## Fix downloads: route SVG/PNG/PPTX through the downloads capability
A plain `<a download>` click or a script-started save only works in the chat's own artifact
preview — it's inert once a page is actually published. Routed all three export paths
through the platform's `downloads` capability instead.

## Add README
Documented what the tool is, how Thea and Drew are designed, and how to redeploy changes.

## Fix missing legend in exports and fragile PPTX loading
The on-screen legend was a separate HTML element never included in exported SVG/PNG files.
Legend now gets baked directly into the exported SVG. Also made the PPTX library load
on-demand with automatic retry, instead of once at page load with no recovery from a
hiccup.

## Port Claude API calls to the published-artifact sample capability
The original tool called `api.anthropic.com` directly, which only works in the chat's own
artifact preview — a published page's CSP blocks that request outright. Replaced it with
the `sample` runtime capability so Thea and Drew actually work once the tool is published
and shared.

## Import Figure Creation Agent (Thea & Drew)
Original tool as authored: an Agile figure-creation assistant pairing Thea (thinking
partner) and Drew (drawing partner), designed as a worked example of the "Casting Call"
gender-conscious persona framework.
