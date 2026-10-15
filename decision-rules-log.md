# Thea & Drew: decision rules log

**Scope:** `index.html`, the Figure Creation Agent (Thea & Drew).
**Started:** Oct 14, 2026. **Last updated:** Oct 15, 2026. Rules are numbered in the order
they were decided. Changes made before this log existed are recorded in `CHANGELOG.md` only.

Each rule gives what was decided, why, what changed, how it was checked, what was not
checked, and the size and SHA-256 of `index.html` after the change.

---

## Rules in force

### R1. The authors' names and contact details are removed from the visible page (TD-21)

- **Rule:** For the submission, nothing a reader of the tool can see names the authors,
  gives their affiliations, or gives their email addresses. This covers the visible page
  only. The same rule as Casting Call's R18.
- **Why:** The tools are submitted without the authors' names. Requested Oct 9 as ALL-04 for
  both tools; split Oct 12 into CC-18 (Casting Call) and TD-21 (this tool).
- **Search:** the page was searched for both authors' first and last names, both email
  addresses, `mailto:`, both institutions, the repo owner's handle, other team members'
  names, and attribution phrases ("the authors", "working paper", "created by"). The
  `<title>`, `<meta>` tags, landing page, example text and the two embedded figures were
  checked too. One place named the authors.
- **The change:**

  | # | Where it shows | Before | After |
  | --- | --- | --- | --- |
  | 1 | End of the consent notice, after "Check journal policy" | a "Questions?" heading and an "Email ..." line with both names and both emails as `mailto:` links | removed in full |

- **Restoring it:** the removed block is kept outside the repo, with a note saying where it
  goes back in (inside the consent notice, after the "Check journal policy" paragraph,
  before the closing `</div>`). It contains the names and emails, so keep it out of the
  repo and out of the submission.
- **Checks:** only those three lines changed; no author name, email or `mailto:` remains in
  `index.html`; `<h3>` and `<div>` tags balance before and after (the page has one more
  opening `<p>` than closing `</p>` both before and after, so the edit did not cause it);
  rendered in headless Chrome, both versions load with no console errors and the consent
  Continue button is present.
- **Published test (Oct 14):** the consent notice ends at "Check journal policy" with no
  names. See Test results.
- **Not changed:** `README.md` (its Contact section, names and emails), `PUBLISHING.md`
  and one older `CHANGELOG.md` entry (a first name each), and the git history still name
  the authors. So do any copies already published.
- **Main page after this change:** `index.html`, 655166 bytes, SHA-256
  `9594ecf56e0f8b400201ea0752288d8c0c23d1bda68aee9801e178b11b3d9d90`. Before: 655356 bytes,
  SHA-256 `5717130e3010a9037655f34aa4c4183c68d291c08d262c87dc1ab03349b60373` (commit
  `936e737`).
- **Enforced by:** page edit only. No effect on prompts to Claude, the diagrams or exports.

### R2. A note on the consent screen says to try another browser if a PDF won't load (TD-10)

- **Rule:** Directly under the sign-in notice, the consent screen says: "If a PDF you attach
  won't load, try a different browser." It is a plain paragraph, not a heading, so it sits
  below the sign-in notice in weight.
- **Why:** Testers saw a console panel with a PDF.js "fake worker" warning. The panel is the
  host's or a browser extension's, not the page's, so the page cannot remove it. Decided
  Oct 3: add guidance instead. The tracker places it beside the sign-in notice (ALL-02) so
  the "before you start" items sit together.
- **The change:**

  | # | Where it shows | Before | After |
  | --- | --- | --- | --- |
  | 1 | Consent screen, under "You'll need to be signed in to Claude to use this tool." | (nothing) | "If a PDF you attach won't load, try a different browser." |

- **Checks:** one line added and nothing else changed; rendered in headless Chrome, the page
  loads with no console errors and the note shows as the second line of the consent screen.
- **Published test (Oct 14):** the note shows under the sign-in notice. See Test results.
- **Not checked:** whether another browser in fact fixes a failed PDF load. The note is
  guidance; TD-11 (configuring the PDF.js worker) is the fix for the warning itself.
- **Main page after this change:** `index.html`, 655236 bytes, SHA-256
  `1edc5cca1fbdb8a3462dd647e8711d1ae7634b952915a36abe339a692737d0e2`.
- **Enforced by:** page edit only. No effect on prompts to Claude, the diagrams or exports.

### R3. A rate limit gets one request and the service's own wording, no auto-retry (TD-08)

- **Rule:** One send makes one request to Claude. When the service rejects it for a rate
  limit, the page shows a wait only if the service's message states one; otherwise it shows
  the service's sentence; otherwise "Too many requests right now — wait a moment and try
  again. Your message was not sent." No number is invented, and nothing is retried
  automatically.
- **Why:** A tester found retrying after a rate limit self-defeating. The tracker's
  diagnosis was of `requestClaude` in an older build (`old/index.html`, no longer in the
  repo), which retried once 1.5 seconds after a 429, so one send became two requests.
- **No change in this log entry.** The current build already meets the rule, since commit
  `823b3b1` (Oct 2): `callClaude` calls `sampleFn` once with no timer or retry, and
  `rateLimitCopy` builds the message as above. This entry records that TD-08 needs no
  further edit.
- **Checks (Oct 14, against stand-in errors, not live):** `rateLimitCopy` and
  `sampleErrorCopy` were taken from `index.html` and run in Node on eight `rate_limited`
  errors. "Try again in 2 minutes." gave "try again in 2 minutes"; "Retry after 500ms" gave
  "try again in 1 second"; "50 requests per minute exceeded" and "you have sent 12 messages
  in 10 seconds" were shown as written, with no wait; "12 requests in 10s; try again in
  60 s" gave "try again in 60 seconds"; "rate_limited", an empty message and a missing one
  gave the fallback line. `callClaude` contains one `sampleFn` call and no `setTimeout` or
  retry.
- **Not checked:** a real rate-limit error from the `sample` capability, so the service's
  actual wording is not known. The Oct 14 published test did not try to trigger one.
- **Main page:** unchanged, `index.html`, 655236 bytes, SHA-256
  `1edc5cca1fbdb8a3462dd647e8711d1ae7634b952915a36abe339a692737d0e2` (as after R2).
- **Enforced by:** `callClaude` and `rateLimitCopy` in `index.html`.

### R4. When the page cannot reach Claude, it says what the reader can do, and never "reload" (TD-20)

- **Rule:** If `window.claude` is missing, the page says "Open this link in a browser where
  you're signed in to claude.ai." If Claude access is missing, it asks for it again at send
  time, and if it is still missing says: "This page can't reach Claude right now. Check that
  you're signed in to claude.ai and allowed Claude access when asked, then press Thea thinks
  or Drew draws again; your last message is kept. If it keeps happening, tell whoever shared
  this link." An error the page has no wording for keeps the service's text and adds "Press
  Thea thinks or Drew draws again; your last message is kept." No message says "reload",
  which loses the intake and the conversation (TD-03).
- **Why:** TD-20. Every send on the public link showed one catch-all line ending "try
  reloading this artifact". The page asked for Claude access once at load; when that came
  back empty, nothing asked again and nothing told the reader what to do.
- **What the page cannot do:** `use('sample')` returns nothing, not an error, when access is
  unavailable, and the reasons (not declared at publish, not offered to this viewer, no
  answer to the prompt) cannot be told apart inside the page. So the tracker's idea of
  keeping the setup error and showing its mapped message is not possible.
- **Diagnosis, Oct 15:** the repo's `index.html` has used `sample` since `18b6391`
  (Sep 29), as has the `vanecon` repo, so there is one build, not two. The tracker's Oct 8
  note that the repo page calls the API directly is out of date. The public Thea & Drew
  link (published Oct 3) uses the same `sample` code. Opened from this account it failed on
  both messages sent, with no "Use Claude" prompt at all, while our private publish, which
  declares `sample`, showed the prompt and worked. With the tracker's report that the link
  fails for its creator too, this points to that publish not declaring Claude access. The
  Claude Code–published link the outside testers used could not be opened from this
  account.
- **Recommended:** republish the public link from the repo's `index.html` as
  `PUBLISHING.md` says, declaring `{"sample": {}, "downloads": true}`. Only the link's owner
  can do this; no change to the page fixes a publish that leaves it out.
- **The change:** `requestSample()` replaces the load-time-only request and is called again
  in `callClaude` when access is missing; `noSampleCopy()` gives the two messages above;
  `sampleErrorCopy`'s default branch adds the retry line.
- **Checks (Oct 15, headless Chrome with a stand-in for `window.claude`, old page vs new):**
  no `window.claude`: catch-all → the "open this link" line; access empty: catch-all → the
  "can't reach Claude" message; access empty at load but present at send: failed every send
  → Thea replied on the first; an unrecognised error: service text alone → service text plus
  the retry line; normal reply: unchanged. Live, in a private publish declaring `sample`:
  the first message got a reply with no error (access had been granted the day before).
- **Not checked:** whether asking again at send time helps in the real runtime, which may
  return the same empty answer it gave at load; the stand-in returned a new one. The new
  messages were not seen live, since access worked.
- **Main page after this change:** `index.html`, 656267 bytes, SHA-256
  `4aebd26e484406d1a7d64f8eaa6259d108baa7d8d3a18dba164cb647f5ffdfca`.
- **Web search (checked Oct 15):** no version of `index.html`, from the first commit
  (`cb1118a`) to this one, has used web search; the first version called the API with
  plain messages and no search tool. So link users have not had a reduced tool, which
  answers the tracker's question. None of R1 to R5 touches the Claude call.
- **Enforced by:** `requestSample`, `noSampleCopy` and `callClaude` in `index.html`.

### R5. No voice input for now; spoken replies stay (TD-19)

- **Rule:** The page offers no dictation. The three microphone buttons (on "How do those
  things relate to each other?", on "Anything else you'd like me to know?", and beside the
  chat box) are gone, with their status lines and the dictation code. Spoken replies stay:
  the header toggle still reads Thea and Drew aloud. Its tooltip is now "Toggle voice
  replies" (was "Toggle voice replies and dictation") and its icon is a speaker (was a
  microphone), so it no longer suggests voice input.
- **Why:** TD-19, confirmed Oct 13 without research-team discussion. Voice input comes back
  only after its transcription quality is assessed and its token cost is measured with
  PRI-02.
- **The change:** removed the three `.mic-btn` buttons, the three dictation status lines,
  `attachDictation` and its three calls, the `SpeechRecognition` check and the `.mic-btn`
  CSS; the voice toggle is disabled only when speech synthesis is unavailable. A comment at
  the voice section says where the dictation code is in the git history. Kept: the
  `.field-row-mic` layout wrapper (the two text boxes now fill its width) and the
  `.mic-status` style, which the file-upload status line still uses.
- **Checks (Oct 15):** headless Chrome with a stand-in for Claude, old page vs new: mic
  buttons 3 → 0, dictation status lines 3 → 0, the voice toggle switches on and off, the
  intake (with both text boxes filled) and a follow-up message both reach Thea, and no script
  or console errors in either. Live, in a private publish declaring `sample`: no microphone
  on the intake or the chat box, the speaker icon in the header, and Thea answered the first
  message.
- **Not checked:** spoken replies with voice switched on in the published page.
- **Main page after this change:** `index.html`, 651904 bytes, SHA-256
  `c789f0c115b3cb96f0bee1cc029a3d054ab441ec0b17b5e323817056a7db2e83`.
- **Enforced by:** page edit only. No effect on prompts to Claude, the diagrams or exports.

### R6. The transcript gets more of the height: the nudge moves into the message box (TD-12)

- **Rule:** The nudge ("Keep thinking with Thea, or have Drew draw it?", or "Keep drawing
  with Drew, or think it through with Thea?" after Drew) is the message box's placeholder,
  not a line of its own above it. The box no longer says "Type a message…". The header is
  slightly tighter, and the send buttons have 12px below them.
- **Why:** TD-12, "the transcript pane is too short"; Vanessa: "please try to fix that." The
  transcript gets whatever height the header and the message area leave. The box is empty
  after every send, which is when the nudge is wanted, so the line above it was spending
  height on text the box can carry.
- **The change:** removed `.composer-nudge` and its `<p>`; `updateNudge` sets the box's
  placeholder; header padding 12px → 9px; `.composer` top padding 8px → 10px; `.send-row`
  padding 8px/4px → 6px/12px. Also removed `.composer-hint`, left unused by R5, and gave the
  send buttons the bottom space that R5's removed status line used to provide (R5 had left
  them 4px from the edge).
- **Measured (headless Chrome, transcript height in px):** window 813: 619 before Oct 15,
  633 after R5, 657 now; window 613: 419, 433, 457; window 463: 269, 283, 307. The fixed
  header and message area together went from 194px to 156px.
- **Checks:** live, in a private publish declaring `sample`: the nudge shows in the box, the
  intake reaches Thea and she answers the first message; the author approved the look in the
  published page.
- **Not checked:** what screen or setup the original complaint came from; the tracker does
  not say. A bigger gain (about 50px more) would need the Thea and Drew buttons beside the
  message box, a design change left for the authors.
- **Main page after this change:** `index.html`, 651806 bytes, SHA-256
  `bd39e29e5d211310ddda8a1e3b360ae8f711d27e3777025a9affd08f7d540cc4`.
- **Enforced by:** page layout only. No effect on prompts to Claude, the diagrams or exports.

---

## Open items

1. `README.md`, `PUBLISHING.md`, an older changelog entry and the git history still name
   the authors. Decide whether the submission needs them removed too (same as Casting
   Call open item 37).
2. A failed request leaves the reader's message in the conversation. `callClaude` adds it
   before the request and does not remove it on an error, so it goes out with the next
   request. The fallback line "Your message was not sent" is then only half true, and a
   reader who retypes the message sends it twice. Found while checking TD-08; not in its
   scope. To be raised as its own tracker item. Seen live on Oct 14 (see Test results):
   after the first request failed, Thea's reply to "Let's keep going." answered the failed
   intake.
3. The first message can fail while the viewer is granting Claude access. In the Oct 14
   test the first request went out while claude.ai's "Use Claude" prompt was open and, once
   access was granted, failed with "Claude access isn't confirmed for this artifact right
   now"; the next message worked. This may be part of TD-20 (Thea fails at first message),
   though TD-20 reports every message failing on every link and only the first failed
   here. The error is not one of the codes `sampleErrorCopy` names, so the reader sees the
   service's text with no hint that sending again works. The error code was not captured.
   R4 adds the retry hint (Oct 15).
4. Replies are shown as plain text, so Markdown from Claude appears literally (Thea's
   `**bold**` shows its asterisks). Not in any tracker item yet.

## Test results

### Published artifact, Oct 14, 2026 (commit `d08fc58`: R1 to R3)

`index.html` from `d08fc58` was published as a private artifact from Claude Code, with the
wrapper stripped and capabilities `{"sample": {}, "downloads": true}` as `PUBLISHING.md`
says, and driven in Chrome by the account that published it.

| Check | Result |
| --- | --- |
| TD-10 note under the sign-in notice | Shown as the second line of the consent screen |
| TD-21: no names in the consent notice | Notice ends at "Check journal policy"; no "Questions?" block |
| `PUBLISHING.md` check 1: tick, Continue | Intake screen opens scrolled to its top |
| First message (intake sent to Thea) | claude.ai's "Use Claude" prompt opened during the call; after it was granted the call failed: "Trouble reaching Claude: Claude access isn't confirmed for this artifact right now" (open item 3) |
| `PUBLISHING.md` check 2: Thea replies | Second message ("Let's keep going.") got a reply in under 10 seconds; it answered the failed intake (open item 2) |

Not tested: Drew, voice, SVG/PNG/PPTX export, PDF or PPTX upload (`PUBLISHING.md` checks
3 to 6), and a real rate-limit error (R3).

## History

| Date | Rule | Change | Tracker |
| --- | --- | --- | --- |
| Oct 14, 2026 | R1 | Authors' names and emails removed from the consent notice | TD-21 |
| Oct 14, 2026 | R2 | Try-another-browser note added under the sign-in notice | TD-10 |
| Oct 14, 2026 | R3 | Recorded that the rate-limit handling already meets TD-08 (`823b3b1`); no page change | TD-08 |
| Oct 14, 2026 | — | Published-artifact test of R1 to R3; open items 3 and 4 added | TD-21, TD-10, TD-08 |
| Oct 15, 2026 | R4 | Clear messages when Claude access is missing; access requested again at send | TD-20 |
| Oct 15, 2026 | R5 | Microphone and dictation removed; voice toggle is replies only, speaker icon | TD-19 |
| Oct 15, 2026 | R6 | Nudge moved into the message box; transcript 38px taller | TD-12 |

## Considered, not built

- Replacing the contact line with a generic address. There is no team address, and the
  task asks only for removal.
- Adding the browser advice to the upload field's "Couldn't read" error as well. That is
  where a reader meets the problem, but the task places the note on the consent screen.
