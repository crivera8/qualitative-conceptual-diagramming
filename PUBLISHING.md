# Merging these changes and publishing the artifact

Written for Vanessa. Two separate jobs: get the code into your repo, then publish a running artifact from your own Claude account so the link belongs to you.

Read `CHANGELOG.md` first if you want to know what changed and why — every commit is there with its reasoning.

---

## Part 1 — merge the changes into your repo

Six files: `index.html`, `README.md`, `CHANGELOG.md`, `PUBLISHING.md`, and two binaries in `figures/` — `sayegh-2025-figure1.png` and `lee-younghyman-2026-figure1.png` (180KB each).

### If there's an open pull request

Easiest path. On the PR page: **Files changed** to review, then **Merge pull request**. Done — nothing to run locally.

### If you'd rather merge it yourself

```bash
git clone https://github.com/vanecon/qualitative-conceptual-diagramming.git
cd qualitative-conceptual-diagramming
git remote add cr https://github.com/crivera8/qualitative-conceptual-diagramming.git
git fetch cr
git log --oneline HEAD..cr/main          # see what's coming
git merge cr/main
git push origin main
```

It should merge cleanly — the commits sit directly on top of your `d237fd7`, and nothing in them rewrites your earlier history.

### Three things worth knowing before you merge

**1. One bug predates all of this and was in your copy too.** Ticking the consent box and pressing Continue appeared to do nothing: the intake screen *was* shown, but centred flex overflow put its top 326px above the scrollable area, where `scrollTop` cannot reach. It overflowed on ordinary desktop windows, not just small ones. Fixed in `33f3bb3`.

**2. The process card has no drawing, on purpose.** It shows the published figure and its citation, and nothing else. Every other card pairs a schematic we drew with a claim line; this one doesn't, because Lee & Young-Hyman's Figure 1 already shows the branching-over-time signature a schematic would have taught, on real constructs rather than on Trigger/Mechanism placeholders. A drawing above it was saying the same thing twice. If you are comparing the three cards and this one looks unfinished, that is why.

(History, in case you hit it in the changelog: this card used to carry a schematic whose branching **contradicted** its own option text, which then read "one shared sequence … in roughly the same way for everyone". The option was reworded in `cc83559` and the schematic is now gone, so that contradiction no longer exists either way.)

**3. Two published figures are embedded in the page, on different permissions.** Sayegh's Figure 1 is on the variance card, Lee & Young-Hyman's on the process card, each with attribution rendered beside it. The licence position of every example is recorded in a comment next to `MODEL_EXAMPLES` in `index.html`, and they are **not** the same:

| Example | Status |
|---|---|
| Sayegh (variance) | Confirmed CC BY 4.0 — reproduced here with attribution |
| Lee & Young-Hyman (process) | CC BY-**NC** 4.0 — reproduced here, *conditional on non-commercial distribution* |
| Shen (not shown) | Confirmed CC BY — *could* be reproduced, deliberately isn't |
| Tilcsik (not shown) | **Not** open access — cite and link only, never reproduce |

The NonCommercial one is worth a moment of your attention, because it is the only thing here that can change after publication. Lee & Young-Hyman's figure is embedded because this tool is given away free. If it ever stops being free — sold, bundled into something sold, or used to promote a commercial offering — that figure has to come out. Sayegh's would not. Nothing in the page enforces this; it is a decision that travels with the licence note in the code.

(Its licence comes from the publisher's CrossRef deposit, not the article PDF, which prints no licence at all — page 1 carries only SAGE's standard "Article reuse guidelines" line. Checked 2026-10-05.)

---

## Part 2 — publish the artifact from your Claude account

### What you need

**Claude Code** — the desktop app, the `claude` CLI, or the VS Code/JetBrains extension. This is not optional, and here is why.

Both personas reach Claude through `claude.use('sample')`, and the export buttons hand files to the viewer through `claude.use('downloads')`. Those are **runtime capabilities granted by the Claude Code artifact host**. They do not exist on GitHub Pages, they do not exist in a file opened from your desktop, and whether they exist in a plain claude.ai chat artifact has not been tested — if you try that route and Thea never replies, this is why. The symptom is a message in the chat reading:

> Thea and Drew talk to Claude through this page's own Claude access, which isn't available right now — try reloading this artifact.

There is no API key to add and no server that fixes it. There is no `api.anthropic.com` call anywhere in this file to route somewhere else.

### Steps

1. **Clone or pull the repo** so you have `index.html` locally.

2. **Open that folder in Claude Code.** In the desktop app: open the folder as your project directory.

3. **Paste this, as-is:**

   > Publish `index.html` as an artifact. It is a complete standalone document, so first strip the document wrapper the artifact host supplies itself: remove the `<!DOCTYPE html>`, `<html>` and `<head>` opening (keep the `<title>` and the `<style>` block) and the closing `</body></html>`. Write that to a separate file so `index.html` stays a valid standalone document, then publish that file with `capabilities: {"sample": {}, "downloads": true}` and a favicon. Do not change anything else in the page.

   The capabilities declaration is the part that matters. Without it both capabilities resolve to `null` at runtime and the tool degrades to a read-only form: the intake renders, nothing replies, nothing exports.

4. **Keep the URL it gives you.** Re-publishing that same derived file later updates the same URL. Publishing a different file path creates a second artifact instead.

### Check it actually works

In order, because each step depends on the last:

1. Gate screen → tick the box → **Continue** → the intake screen appears, scrolled to its top.
2. Fill in an outcome, press **Thea thinks** → she replies within a few seconds. *If she doesn't, the capabilities declaration is missing — go back to step 3.*
3. Click **Voice off** in the header to turn it on, then send another message. Thea should read in a female-sounding voice, Drew in a male-sounding one.
4. Ask Drew to draw something with a feedback loop — "A increases B, and B feeds back into A over time". You should see **two separated curved arrows**, not one.
5. On the diagram card, click **SVG**, then **PPTX**. Both should offer you a file. In the PPTX, the feedback pair should be two parallel lines.
6. Attach a PDF or PowerPoint of a figure under "Already have a draft of the figure?" and confirm it reports pulling text out.

Steps 4, 5 and 6 are the ones never verified end-to-end — their geometry and wiring are tested, but nobody has run them inside a published artifact. If something is wrong, it will be there.

### Sharing it

Artifacts are **private to you** when published. Nobody else can open the link until you share it, from the **Share** menu on the artifact page itself. That menu is also the only place sharing can be changed — it can't be set at publish time.

---

## If you only want to look, not publish

`index.html` opened straight from disk gives you the full consent screen, the intake form with the three example cards and their schematics, and the methodology — everything except Thea and Drew replying, and the exports. That is enough to review the copy and the figures without publishing anything.
