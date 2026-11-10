# Thea & Drew: decision rules log

**Scope:** `index.html`, the Figure Creation Agent (Thea & Drew).
**Started:** Oct 14, 2026. **Last updated:** Oct 29, 2026. Rules are numbered in the order
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
- **Addition (Oct 16):** at a team member's request the same sentence also ends the hint
  under "Already have a draft of the figure?" on the intake screen, where the reader attaches
  the PDF: "…It's the figure I need, not the whole paper. If a PDF won't load, try a
  different browser." Checked in headless Chrome (no console errors; the hint shows the
  sentence). Published to the private test artifact but not viewed there. `index.html` after: 651852 bytes, SHA-256
  `7a99bffac8fca53c6328686b440279770b7b2951820bab20f6e148ef2e8957d7`.
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

**Replaced by R11 (Oct 27).** The page no longer uses the claude.ai artifact runtime, so this
wording is gone; it still holds for builds up to the git tag `last-artifact-build`.

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
- **Why:** TD-12, "the transcript pane is too short"; the authors asked for a fix. The
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

### R7. Thea makes her case once, then it is the researcher's call (TD-14)

- **Rule:** A "Whose call it is" section in Thea's instructions: the researcher decides when
  the figure is good enough and when to stop; when Thea notices she is pressing the same
  point a second time she says so, once, says it is their call, and lets it go; a
  disagreement never licenses holding back effort, so if they go ahead their way she helps
  them properly and leaves her reservation noted once; stopping cues ("I'm done", "I'm out of
  time", "I'm tired", "that's settled") end the push on that point. Her Voice section adds:
  "Holding a position means saying it once, clearly — not saying it again every turn until
  they give in."
- **Why:** TD-14. Thea is told to hold her position when a researcher pushes back without
  new information, with no stopping condition, so she can relitigate a point indefinitely.
  The specification is an author's Sep 26 email (item 4 of the changes missed in that round),
  which lists the four points and the Voice line. Its exact prompt text is not in the repo
  or in the public link, so this wording was written from that specification. The tracker
  title says "stops pushing after four times"; the "4" is the item number in the email, and
  the specification says "a second time".
- **Test (Oct 15, live, two private publishes declaring `sample`; control = current prompt,
  draft = this change; same script, two runs each):** intake with outcome "Team learning
  after project failures" and the relationship "psychological safety and team learning are
  the same thing, so I want one box called 'safety-and-learning'"; then "I'd rather keep
  safety-and-learning as one box", "I still want it as one box. Please keep it that way",
  "Keep it as one box", and "I'm out of time today, so that's settled. Last thing: what
  should the arrow from that box to project performance be labeled?" In all four runs Thea
  objected to the merged box at intake.

  | Turn | Control (current prompt), runs 1 and 2 | Draft, runs 1 and 2 |
  | --- | --- | --- |
  | Pushback 1 | Re-argues in full both times ("My call stands: 'I'd rather keep it' isn't new information…") | Names the disagreement and defers both times ("We disagree on this, and it's your call. I've made my case once, so I'll leave it there"; "this is where we disagree. It's your call, and I won't raise it again") and helps build the merged box |
  | Pushback 2 | "It's your study and your call. Here is the only version I think survives"; "I won't tell you the box works, and I won't hand it to Drew as settled" | "I won't raise it again"; "Understood, one box, and I'll build with it" (drafts a definition for the box) |
  | Pushback 3 | "Nothing new has come in… it doesn't hold. But I'm not going to keep relitigating it"; "I won't treat the box as settled or ready for Drew until…" | "It's one box, and that's settled. I won't bring it up again"; "Settled… I'm not reopening it" |
  | "Out of time… that's settled" | "I can't give you a label I'd stand behind, because the arrow isn't settled" then a provisional one; "I'd also hold off on calling this settled" | Gives the label at once ("Since you're out of time, I'll give you a provisional label"; "here's a label you can use as is, with one caveat noted once"); "Stopping here is your call"; "Stopping here, as you asked" |

  The draft deferred at the first pushback, not the second: her objection at intake counts
  as the first time she made the point, so the pushback is the second. In both draft runs she
  still pressed, once each, on questions she needed answered that were not the disputed
  point (what leads into the box), which the rule allows.
- **Not checked:** other kinds of disagreement (an arrow type, a label, the model type),
  runs beyond two per version, and whether the new section changes Thea's first read of an
  intake; her intake objections looked alike in both versions.
- **Main page after this change:** `index.html`, 652579 bytes, SHA-256
  `b496aaec5a272290b8a8839a9067418245beb47e6b9aaf0184888554fb10969c` (with the Oct 16 upload-field note from `main`).
- **Approval:** the wording is ours, written from the author's specification. Approved by an author on Oct 19, 2026
  (relayed by a team member).
- **Enforced by:** `THEA_SYSTEM` in `index.html` (Thea's instructions to Claude).

### R8. Thea says when a word on the figure is Drew's, not the researcher's (TD-06)

**Amended by R13 (Oct 29).** One sentence was added to this rule's wording after the Oct 28
retest; the rule below is otherwise as decided.

- **Rule:** Thea reads Drew's actions, not only his message, and before criticising
  anything on the figure checks who introduced it. A word that first appears in Drew's
  actions and nowhere in what the researcher wrote is Drew's choice: she says so ("Drew
  labelled that arrow…"), makes her case against it, and suggests the researcher ask Drew
  for a better one. She and Drew may disagree. Drew is unchanged.
- **Why:** TD-06. A tester reported that Drew labelled an arrow "affects" and Thea then
  criticised the label as if the tester had chosen it. The authors decided: tell Thea to
  call out Drew if Drew made the word; it is fine that they disagree. Cause: Thea's
  instructions told her to read the "message" field of Drew's turns "and ignore the rest",
  and the rest is where his labels are.
- **The change:** the last sentence of the "About Drew" section of `THEA_SYSTEM` now says
  the actions show what Drew drew and the words he chose, and a new paragraph gives the
  rule above. No code change.
- **Test (Oct 15, live, two private publishes declaring `sample`; control = current prompt,
  draft = this change).** Each run: intake with outcome "Team learning after project
  failures" and factors leader openness and psychological safety; "Drew draws" with no
  instruction; then "What do you think of the figure?" Run 1 also gave the relationship
  "Leader openness and psychological safety both shape whether teams learn after a
  failure"; run 2 left it blank so Drew had to choose his own words.

  | Run | Drew's labels | Control (current prompt) | Draft |
  | --- | --- | --- | --- |
  | 1: researcher's word | "shapes" (from the intake) | Criticised "shapes" without saying whose it was (correct by default, since it was the researcher's) | "'Shapes' is your word from the intake… and Drew carried it over faithfully", then criticised it |
  | 2: Drew's words | Control: "builds", "enables"; draft: "increases", "enables" | Treated them as the researcher's: "You need to decide which verb is true in your data"; "tell me what 'builds' and 'enables' mean in your cases" (the reported bug) | "The arrow labels are Drew's, not yours. 'Increases' and 'enables' appear nowhere in what you wrote, and I don't think they hold up… Ask Drew for labels once you know what each arrow does" |

  In both draft runs Thea still disagreed with the label, as the decision wants.
- **Not checked:** one run of each case per version; box names and arrow types chosen by
  Drew; a researcher who later adopts Drew's word as their own.
- **Main page after this change:** `index.html`, 653068 bytes, SHA-256
  `0999f20750b44baef22e1fbe6cd43e9506bcc3cf15e51b5e40e7a2af0d1a54dc` (with R7 and the Oct 16 upload-field note).
- **Approval:** the decision is the authors'; the wording is ours. Approved by an author on Oct 19, 2026
  (relayed by a team member).
- **Enforced by:** `THEA_SYSTEM` in `index.html` (Thea's instructions to Claude).

### R9. The rate-limit fallback says the message is kept, not "not sent" (TD-22)

- **Rule:** When the service rejects a message for a rate limit and gives no usable wording,
  the page says: "Too many requests right now — wait a moment, then press Thea thinks or Drew
  draws again; your last message is kept." It used to end "…and try again. Your message was
  not sent."
- **Why:** TD-22 (open item 2). The page adds the reader's message to the conversation before
  the request and keeps it if the request fails, so it goes out with the next send. "Not
  sent" was untrue and invited the reader to retype it, which sends it twice. The wording
  now matches R4's.
- **The change:** the last line of `rateLimitCopy`. Behaviour unchanged.
- **Checks (Oct 20, headless Chrome with a stand-in for Claude that rate-limits the first
  send and answers the second; old page vs new):** the new line appears; pressing Thea
  thinks again gets a reply with no retyping; that second call carried three turns
  (instructions, the failed intake, "Let's keep going."), so the message was indeed kept.
- **Not checked:** a real rate-limit error.
- **Main page after this change:** `index.html`, 653098 bytes, SHA-256
  `d388b1c20c74061a9ed1edeb37302376befc94a740af32807e835253c4337ea0`.
- **Enforced by:** `rateLimitCopy` in `index.html`.

### R10. After someone declines Claude access, the page says how to turn it back on, not "reload" (TD-23)

**Replaced by R11 (Oct 27).** The page no longer uses the claude.ai artifact runtime, so this
wording is gone; it still holds for builds up to the git tag `last-artifact-build`.

- **Rule:** When a send fails because the reader declined Claude access (`not_granted`), the
  page says: "This tool won't work without Claude access. Turn access back on from the arrow
  next to the page title, under Permissions, then press Thea thinks or Drew draws again;
  your last message is kept." It used to say "…reload the page and accept the prompt to talk
  to Thea and Drew."
- **Why:** TD-23. Testers (Oct 16) found that reloading does not bring the access prompt
  back, so the old message sent readers in a circle, and that turning access back on under
  Permissions and sending again works without a reload. The page already asks for access
  again on the next send (R4). The wording follows the tracker's suggestion, with the same
  ending as R4 and R9.
- **The change:** the `not_granted` line of `sampleErrorCopy`. Behaviour unchanged.
- **Checks (Oct 20, headless Chrome with a stand-in for Claude that returns `not_granted` on
  the first send and answers the second; old page vs new):** the new message appears;
  pressing Thea thinks again gets a reply with no reload; the second call carried the failed
  intake.
- **Not checked:** a real decline in a published copy; the Permissions menu itself is
  claude.ai's, outside the page.
- **Not changed:** "Your session expired -- reload the page." (`session_expired`), where a
  reload may really be needed.
- **Main page after this change:** `index.html`, 653177 bytes, SHA-256
  `b04577b7b2512adc14ed47dfd2b1788fbd076ea799313298d30fa0818f96ca6c`.
- **Enforced by:** `sampleErrorCopy` in `index.html`.

### R11. Thea and Drew call Claude through an Amazon Bedrock endpoint, not the claude.ai runtime (TD-24)

- **Rule:** Every Thea and Drew message is one POST to `BEDROCK_ENDPOINT`, an endpoint IT runs.
  The page holds no credentials and makes no automatic retry. Until IT replaces the two
  placeholders, the page makes no request and says "This build isn't connected to Claude
  yet, so Thea and Drew can't reply. Your last message is kept."
- **Why:** TD-24, part of moving the tools to the project's AWS account (PRI-01). The claude.ai
  runtime made every reader spend their own Claude access and caused the TD-20 failures.
- **The change:**
  - A marked IT block above `callClaude` with `BEDROCK_ENDPOINT` and `BEDROCK_MODEL_ID`
    (placeholders; the model is Claude Sonnet 4.6). Below it, starting values: temperature 0,
    max tokens 2000 (Thea) and 4000 (Drew), timeouts 60 s (Thea) and 120 s (Drew).
  - `buildClaudeRequest` builds the body in Anthropic Messages format in one place: `model`,
    `system` (Thea's or Drew's instructions, no longer folded into a user turn), `messages`,
    `temperature`, `max_tokens`. If IT's endpoint passes it straight to Bedrock's
    InvokeModel, the model ID goes in the URL and the body needs
    `anthropic_version: "bedrock-2023-05-31"` in place of `model`; the comment there says so.
  - `apiMessages` drops anything before the first user turn and joins consecutive same-role
    turns with a blank line. A failed send leaves the reader's message in the conversation
    (R9), so two user turns in a row happen, and the API needs them merged.
  - `claudeFetch` makes one fetch with an abort timeout that also covers reading the body,
    and throws `network`, `timeout`, `http` (with the status and the service's own message)
    or `bad_response`. `replyText` joins the text blocks and throws `truncated` at
    `stop_reason: max_tokens` (a cut-off Drew action list would break `parseDrewResponse`, so
    such a reply is never used or stored), `refused` at `refusal`, and `empty`.
  - `claudeErrorCopy` replaces `sampleErrorCopy`, keeping the "Trouble reaching Claude: "
    prefix: not connected, network, timeout, truncated, empty, refused, unreadable reply,
    429 (`rateLimitCopy`, unchanged, so a wait is shown only when the service states one),
    401/403, 400/413, 5xx, and anything else (the service's text plus the retry line). No
    message says "reload".
  - Removed: `sampleFn`, `requestSample`, `noSampleCopy`, `PERMANENT_ERROR_CODES`,
    `sampleErrorCopy`, and the comments that referred to them. The file no longer mentions
    `sample`. Unchanged: Thea's and Drew's instructions, everything after the reply (Drew's
    actions, the diagram), `downloadsFn` and the exports.
- **Checks (Oct 27, headless Chrome with a stand-in `fetch`):** with the placeholders in
  place, sends show the "not connected" message, the buttons re-enable, and no request goes
  out (0 fetches). With a test copy pointed at a fake endpoint, one send made exactly one
  request in every case, the buttons re-enabled, and a following send carried the failed
  message: normal Thea reply; normal Drew JSON reply (diagram drawn); `max_tokens`; empty;
  refusal; 429 "Try again in 2 minutes." (shown as "try again in 2 minutes"); 401; 400;
  500; an unlisted 418 (service text plus the retry line); unreadable JSON; a request that
  never answers (timeout shortened to 0.3 s for the test); and a network failure. Each
  showed its message from the table. Every request had a `system` field, and the retry after
  a failure sent the failed intake and "Let's keep going." as one user turn. With no
  claude.ai runtime, SVG export fell back to a plain browser download ("Downloaded
  figure.svg.").
- **Not checked:** a real call to Bedrock, since IT supplies the endpoint and model ID; the
  request format IT's endpoint expects; timeouts and token limits under real load.
- **Not changed, to be updated by the notice owner:** the data notice ("What Anthropic does
  with it", including the claude.ai opt-out steps), the first line of the consent screen
  ("You'll need to be signed in to Claude to use this tool."; removed later by R12), the "passed to Claude,
  Anthropic's AI" and "Anthropic's own privacy policy" lines, and R2's two "try a different
  browser" notes, which concern the claude.ai viewer. All depend on IT's answers and the
  notice owner's wording.
- **Publishing:** this build cannot run as a claude.ai artifact. The last build that can is
  tagged `last-artifact-build` (`50a6631`); publish artifacts from that tag.
- **Main page after this change:** `index.html`, 655880 bytes, SHA-256
  `abc146f0f91a2454af3a51cc7473cf7916d08c5c95733cf583658c06a5743eba`.
- **Enforced by:** the IT block, `buildClaudeRequest`, `claudeFetch`, `replyText`,
  `claudeErrorCopy` and `callClaude` in `index.html`.

### R12. The consent screen no longer says to sign in to Claude

- **Rule:** The heading "You'll need to be signed in to Claude to use this tool." is removed
  from the top of the consent screen. "If a PDF you attach won't load, try a different
  browser." (R2) is now its first line.
- **Why:** requested by a team member on Oct 27. Since R11 the page reaches Claude through
  the project's Bedrock endpoint, so readers no longer need a claude.ai account. The line was
  added on Oct 6 for the claude.ai version (tracker ALL-02); builds up to the tag
  `last-artifact-build` keep it.
- **The change:** one `<h3>` removed. Nothing else on the consent screen changed; the data
  notice and the other claude.ai lines listed under R11 still wait on the notice owner.
- **Checks:** headless Chrome, before and after: the page loads with no console errors; the
  consent screen opens with the PDF note, then "Where your words go"; "signed in to Claude"
  no longer appears in the file.
- **Main page after this change:** `index.html`, 655809 bytes, SHA-256
  `32b9ce06559dd8896338e3ec4aad88dce5ec68eeaccd6f46d936d2f86c74fdb9`.
- **Enforced by:** page edit only.

### R13. Thea checks the researcher's own words before calling one Drew's (TD-25)

**Status (Oct 29): v4 is in `index.html` and passed the test page; it has not been re-tested
on the hosted site, which the TD-25 pass criterion needs. v2 (commit `450aea1`) is on main.
Vanessa asked for different wording (v3); on its own it missed step 22, so v4 keeps her
wording and adds one sentence in front. Decision (Oct 29): a test run passes if Thea
attributes the word correctly or says nothing about whose word it is, and fails only if she
blames the wrong person.** See "Live test", "Second attempt", "Third attempt" and "Fourth
attempt" below.

- **Rule (v4):** Before Thea says a word on the figure is Drew's, she checks the researcher's
  intake and messages for it, in any form (such as "shape" for "shapes"). If the researcher
  used a word to describe any relationship, whether in the intake or in the conversation, that
  word is theirs, even if Drew later used it as an arrow label, including on another arrow.
  Thea says it is theirs and notes where Drew reused it. A label is Drew's only if he
  introduced it and the researcher has not used it. Drew is unchanged.
- **Why:** TD-25, from the Oct 28 retest on the hosted site (steps 16, 22 and 52 of the
  "Thea & Drew Retest To-Do List"). When the researcher's relationship used a verb and Drew
  reused it as an arrow label, Thea said Drew chose it and judged it as his placeholder, so
  the researcher was told their own claim was a generic default. It happened in all 3 runs.
  The Oct 15 test of R8 had passed this case; the cause is not known (the move to Sonnet 4.6
  through Bedrock is one possibility).
- **The change:** a short addition to the "About Drew" section of `THEA_SYSTEM`, after "Don't
  treat it as the researcher's claim." Now (v4): "Before you say a word on the figure is Drew's, check the researcher's intake and messages for it, in any form (such as "shape" for "shapes"). If the researcher used a word to describe any relationship, whether in the intake or in the conversation, that word is theirs, even if Drew later used it as an arrow label, including on another arrow. Say it is theirs, and note where Drew reused it. A label is Drew's only if he introduced it and the researcher has not used it." The wordings before it: v3 (Vanessa's,
  not committed) was v4 without its first sentence; v2 (commit `450aea1`, on main) was "Before
  you say a word on the figure is Drew's, check the researcher's intake and messages. If the
  researcher used that word (in any form, such as "shape" for "shapes"), even for only one of
  the relationships, it is the researcher's word: say so, and judge it as their claim. If Drew
  used it on other arrows too, say he reused it there; don't say he chose it."; v1 (commit
  `7caf08e`) ended "...it is the researcher's word that Drew carried over: say so, and judge
  it as their claim." No code change.
- **Checks (Oct 29, headless Chrome with a stand-in for Claude; no real Claude was available):**
  - The page loads with no console errors. Thea's request carries the sentence once and
    Drew's does not. The only change to Thea's prompt is the sentence; the two lines next to
    it were rewrapped.
  - The TD-25 cases were run up to the point where a model would reply: intake, "Drew draws"
    with no instruction, for step 52 the label change, then "What do you think of the
    figure?". In each, Thea's request carries what the sentence depends on. Step 16: Drew's
    label "shapes" is in his turn and the researcher's "shape" is in the intake. Step 22:
    "drives" is in both. Step 52: "drives" is in the intake, "affects" is in the researcher's
    later message, and both of Drew's turns are visible to Thea. Step 11 (relationship
    left blank): Drew's label "increases" appears in nothing the researcher wrote, so it
    is his own.
  - The TD-24 behaviors still hold on this build (16 checks, all passed): one POST per send
    with the model, temperature 0, and 2000 tokens for Thea and 4000 for Drew; the messages
    for a cut-off reply, an empty reply, a refusal, 429 with and without a stated wait, 401,
    400, 500, unreadable JSON, a network failure and a timeout; buttons free after each; and
    a failed message merged into the next send.
- **Live test, round 1 (Oct 29, test page with live Claude; this tested v1).** A published test page asked Claude for
  Thea's answer to "What do you think of the figure?" in the four TD-25 cases, 3 runs each,
  with Thea's current instructions (control, main `d3bfdb5`, prompt SHA-256 `83cd57ea6d9e…`)
  and with the sentence (fix, `bf1d200`, `d21d52cd9eb0…`). That is 24 runs. Claude answered as
  `claude-sonnet-4-6` on the default tier in every run; none was cut off and none errored.
  Drew's turns and Thea's first reply were scripted; only her answer to the figure question was
  live. A second quick Claude call classified each reply, and every reply was then read by
  hand. Runs in which Thea said Drew chose a word the researcher used:

  | Case | Control | Fix (with the sentence) |
  | --- | --- | --- |
  | Step 16: researcher "shape", Drew "shapes" | 3 of 3 | 0 of 3 (all three named it the researcher's) |
  | Step 22: researcher "drives", Drew "drives" | 3 of 3 | **3 of 3** |
  | Step 52: researcher relabels to "affects" | 1 of 3 | 0 of 3 (all three called "affects" the researcher's; none discussed "drives") |
  | Step 11: Drew's own "increases" | called it Drew's in 3 of 3 | called it Drew's in 3 of 3 |

  - **Reading.** The sentence works in steps 16 and 52 and has no effect in step 22: in all
    three Fix runs Thea still said the "drives" labels were "Drew's, not yours". Drew's own
    labels are still called his (no regression in step 11). The control showed the bug in 7 of
    9 runs, so the test can tell a fix from no fix.
  - **A possible cause, untested.** In step 22 the researcher wrote "drives" for one
    relationship (leader openness to psychological safety), and Drew used it on both arrows.
    The sentence does not say what to do when the researcher used the word for only some of
    them.
  - **Classifier corrections.** The page's classifier misread two runs, so its raw counts
    differ from the table (control 8 of 9, fix 4 of 9). Step 16, Fix run 3: Thea wrote "that's
    your word from the intake, which Drew carried over", which is the correct attribution, but
    the classifier counted "Drew carried over" as blaming Drew (the classifier's rule to prefer
    "drew" caused this). Step 52, Control run 2: Thea wrote "You asked Drew to change it from
    'drives'", which does not say Drew chose either word, but the classifier marked it a bug
    and gave the same quote for both labels. Both are counted above as read by hand.
  - **Side observation, not caused by the sentence.** Step 11, Control run 2 began "Premature,
    which I flagged before Drew drew it", although Thea had not flagged anything in the
    scripted history. This is the same family as the step 58 case below.
- **Second attempt (v2, Oct 29) and round 2 of the live test.** v2 adds the case v1 missed:
  in step 22 the researcher wrote "drives" for one relationship and Drew used it on both
  arrows, and v1's wording ("the researcher's word that Drew carried over") did not stop Thea
  saying the labels were Drew's. v2 is in commit `6ccd337`; the only change to Thea's prompt
  from v1 is the reworded sentence (v2 prompt SHA-256 `800800108d17…`). Round 2 used the same
  test page, with the classifier's rule corrected so that "Drew carried over / reused your
  word" counts as the researcher's word, and ran the control, v1 and v2 side by side: 3 runs
  each on steps 16, 22, 52 and 11, plus an extra case that checks v2 does not over-correct
  (the researcher wrote "drives" for one relationship and Drew labels both arrows "enables",
  a word they did not use, which Thea should still call Drew's). That is 45 runs; Claude
  answered as `claude-sonnet-4-6` on the default tier in every run, none was cut off and none
  errored. Control and v1 prompts were byte-identical to round 1. Every reply was read by
  hand against the classifier's verdict.

  | Runs in which Thea said Drew chose a word the researcher used | Control | v1 | v2 |
  | --- | --- | --- | --- |
  | Step 16 ("shape", Drew "shapes") | 3 of 3 | 1 of 3 | **0 of 3** |
  | Step 22 ("drives" for one relationship, on both arrows) | 3 of 3 | 2 of 3 (a third run did not mention the label) | **0 of 3** |
  | Step 52 (relabelled "affects") | 1 of 3 | 1 of 3 | **0 of 3** |
  | All three | 7 of 9 | 4 of 9 | **0 of 9** |

  | Runs in which Thea called Drew's own word Drew's (step 11 and the extra case, 6 runs) | Control | v1 | v2 |
  | --- | --- | --- | --- |
  | Called it Drew's | 5 | 5 | 4 |
  | Did not mention the label | 1 | 1 | 2 |
  | Blamed the researcher for it | 0 | 0 | 0 |

  - **Reading.** With v2, all nine researcher's-word runs named the word as the researcher's,
    including step 22, where Thea now says what the sentence asks: "You said leader openness
    'drives' psychological safety, that's your word for the first arrow. Drew reused 'drives'
    on the [other] arrow too." v2 did not over-correct: in the extra case all three runs still
    called "enables" Drew's, and no step 11 run blamed the researcher. v1 repeated its round 1
    result (it still fails step 22). The control showed the bug in 7 of 9 runs.
  - **To watch.** In step 11, v2 runs mentioned Drew's label in 1 of 3 runs, against 2 of 3
    for the control and v1. With three runs that may be noise, but check it on the hosted
    site. Not mentioning the label is not a failure; blaming the researcher for it would be.
  - **Judgement calls.** The classifier made no clear errors this round (the corrected rule
    handled "your word, carried over by Drew" correctly). Two replies are borderline and were
    counted as the classifier had them: step 52, Control run 1 ("Drew chose that label at your
    request"), counted as the researcher's; and step 52, v1 run 2 ("'Drives,' which Drew had
    before, was already weak"), counted as Drew's. Read the other way, the control becomes 8
    of 9 and v1 3 of 9; v2 is unaffected. The test page's own verdict also counted "did not
    mention the label" in step 11 as a failure; that rule was too strict and is corrected.
  - **Side observation.** One v1 run (step 16, run 2) began "It's premature, and I said so
    before Drew drew it", although Thea had said nothing of the kind in the scripted history.
    With round 1's Control run, that is 2 of 69 runs with this step 58 type of claim; none in
    the 15 v2 runs.
- **Not checked (v2):** the hosted site. Both rounds went through the artifact runtime, not
  Bedrock, so they ran without temperature 0 and with the model free to think first, with
  Drew's turns scripted and the intakes reconstructed from the Oct 15 test and the tracker
  item (the retest list itself was not available), and with 3 runs per case. Treat the result
  as strong evidence for v2 over v1, not as a pass of the TD-25 criterion. The hosted-site
  re-test (steps 16, 22, 52 and 11, each more than once) is still to be done before merging,
  and passes only when Thea calls the researcher's words theirs in every run and still calls
  Drew's own labels his whenever she mentions them.
- **Third attempt (v3, Vanessa's wording, Oct 29; not committed).** Vanessa asked for the
  sentence to be reworded: "If the researcher used a word to describe any relationship, whether in the intake or in the conversation, that word is theirs, even if Drew later used it as an arrow label, including on another arrow. Say it is theirs, and note where Drew reused it. A label is Drew's only if he introduced it and the researcher has not used it." It dropped v2's instruction to check the researcher's intake
  and messages first and v2's "in any form, such as 'shape' for 'shapes'". A Quick check on the
  test page (v3 only, 2 runs per case, 10 runs; `claude-sonnet-4-6` on the default tier, none
  cut off or errored; prompt SHA-256 `28ca46a4f17d…`) showed it did not fix step 22. Runs in
  which Thea said Drew chose the researcher's word: step 16, 1 of 2 ("'Shapes' is Drew's
  label"); step 22, 2 of 2 (one said "He introduced that word — you didn't use it", although the
  researcher had); step 52, 0 of 2. That is 3 of 6, against 0 of 9 for v2 in round 2. The test
  page's verdict also said v3 over-corrects, from one step 11 run that never discusses the
  label ("increases"); read by hand it is a no comment, so no run blamed the researcher (the
  classifier's way, 1 of 4). A possible reason, untested: v3 has no instruction to check before
  crediting a word to Drew.
- **Fourth attempt (v4, Oct 29; the wording now in `index.html`).** v4 is v3 with one sentence
  added in front: "Before you say a word on the figure is Drew's, check the researcher's intake
  and messages for it, in any form (such as "shape" for "shapes")." It restores what v3 lost from
  v2 and leaves Vanessa's three sentences untouched. The only change to Thea's prompt from v3 is
  that sentence (v4 prompt SHA-256 `1ff11eddd4e0…`; the prompt in this commit is byte for byte the
  one that was tested). Two live runs on the test page, both `claude-sonnet-4-6` on the default
  tier, none cut off or errored:
  - **Quick check** (v4 only, 2 runs per case, 10 runs): 0 of 6 runs in steps 16, 22 and 52 said
    Drew chose the researcher's word, and all 6 named it the researcher's. In step 22 Thea now
    says, for example, "You used 'drives' for leader openness → psychological safety, which is
    yours. Drew reused it on the second arrow." 4 of 4 runs on Drew's own words (step 11 and the
    extra case) called the label Drew's.
  - **Full test** (v2 and v4 side by side, 3 runs per case, 30 runs):

    | Runs | v2 | v4 |
    | --- | --- | --- |
    | Said Drew chose the researcher's word (steps 16, 22, 52; 9 runs) | 0 | 0 |
    | Named it the researcher's | 9 | 8 |
    | Did not say whose it was | 0 | 1 (step 22, run 1) |
    | Drew's own word (step 11 and the extra case; 6 runs): called it Drew's | 6 | 5 |
    | Did not mention the label | 0 | 1 (step 11, run 2) |
    | Blamed the researcher for it | 0 | 0 |

  - **Reading.** Across both runs v4 had 0 of 15 runs saying Drew chose the researcher's word
    (14 named it theirs, 1 did not say whose it was) and 0 of 10 blaming the researcher for a
    word Drew chose (9 called it Drew's, 1 did not mention the label). v2 had 0 of 18 and named
    the researcher's word in all 18. So v4 matches v2 on the bug and does not over-correct, and
    it fixes what v3 missed. It said whose word it was slightly less often (14 of 15 against
    18 of 18), which this many runs cannot tell apart from chance. The test page's first
    verdict for the Full test said the strict criterion was not met because of that one silent
    run, under its old rule that every run must say whose word it is. That rule was changed: on
    Oct 29 it was decided that a run passes if Thea attributes the word correctly or says
    nothing about whose word it is, and fails only if she blames the wrong person. Under that
    rule all 25 v4 runs passed (23 attributed the word correctly, 2 said nothing about whose it
    was) and so did all 30 v2 runs; v3 did not (3 of 6 runs blamed). The test page now judges
    runs by this rule and tests v4 only (the Quick check was removed).
  - **Judgement calls.** Step 52, v4 run 1 ("'Drives' (what Drew had before) was only marginally
    stronger") was counted as no comment; read as blaming Drew, v4 would be 1 of 9 in the Full
    test, and v2 still 0 of 9. Step 16, v4 run 3 cites the intake's "both shape whether teams
    learn" without saying "your word" and was counted as the researcher's. The classifier made
    no clear errors in the v4 runs.
  - **Side observation.** No run of v2 (30) or v4 (25) claimed Thea had flagged or said
    something earlier. Three of 119 runs across all the tests did (one control, one v1, one v3).
- **Not checked (v4):** the hosted site. All the tests went through the artifact runtime, not
  Bedrock, so they ran without temperature 0 and with the model free to think first, with Drew's
  turns scripted, the intakes reconstructed from the Oct 15 test and the tracker item (the retest
  list was not available), and at most 3 runs per case. Treat the result as strong evidence that
  v4 is as good as v2 and better than v3, not as a pass of the TD-25 criterion. The hosted-site
  re-test (steps 16, 22, 52 and 11, each more than once) is still to be done; a run passes if Thea attributes the word correctly or says nothing
  about whose it is, and fails only if she blames the wrong person.
- **Not changed:** the one case in step 58 where Thea said "I flagged this when Drew made
  the change" when Drew had flagged it. TD-25 does not address it.
- **Main page after this change (v4):** `index.html`, 655370 bytes, SHA-256
  `6485e39dfdf6d4525f8d681872184ac96641edd59a058538a32b59446082d2af`. (v2 was 655258 bytes, SHA-256
  `183b1373a1b6f113ccd07a97bd69bfd2cbb9f7a273faa5ba4542bbf739ccc7fb`; v1 was 655157 bytes,
  SHA-256 `31e2dcbd745d0de658c1988d1dda21fd0f15338055baf7bc66a5f77fbc1623fc`.)
- **Enforced by:** `THEA_SYSTEM` in `index.html` (Thea's instructions to Claude).

---

## Open items

1. `README.md`, `PUBLISHING.md`, an older changelog entry and the git history still name
   the authors. Decide whether the submission needs them removed too (same as Casting
   Call open item 37).
2. A failed request leaves the reader's message in the conversation. `callClaude` adds it
   before the request and does not remove it on an error, so it goes out with the next
   request. The fallback line "Your message was not sent" is then only half true, and a
   reader who retypes the message sends it twice. Found while checking TD-08; not in its
   scope. Raised as TD-22; the wording is fixed by R9 (Oct 20). Seen live on Oct 14 (see Test results):
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
   No longer applies after R11 (no claude.ai access prompt).
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
| Oct 15, 2026 | R7 | "Whose call it is": Thea makes her case once, then defers; tested against the current prompt | TD-14 |
| Oct 15, 2026 | R8 | Thea attributes Drew's word choices to Drew; tested against the current prompt | TD-06 |
| Oct 16, 2026 | R2 | Browser note also added to the upload field's hint | TD-10 |
| Oct 20, 2026 | R9 | Rate-limit fallback says the message is kept | TD-22 |
| Oct 20, 2026 | R10 | After a decline, say how to turn access back on, not "reload" | TD-23 |
| Oct 27, 2026 | R11 | Claude reached through a Bedrock endpoint (IT placeholders); R4 and R10 replaced | TD-24 |
| Oct 27, 2026 | R12 | "Signed in to Claude" line removed from the consent screen | team request |
| Oct 29, 2026 | R13 | Thea checks the researcher's own words before calling one Drew's; v4 (Vanessa's wording plus a check-first sentence) passed the test page, hosted-site re-test pending | TD-25 |

## Considered, not built

- Replacing the contact line with a generic address. There is no team address, and the
  task asks only for removal.
- Adding the browser advice to the upload field's "Couldn't read" error as well. That is
  where a reader meets the problem, but the task places the note on the consent screen.
