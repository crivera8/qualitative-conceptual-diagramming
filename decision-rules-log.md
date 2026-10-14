# Thea & Drew: decision rules log

**Scope:** `index.html`, the Figure Creation Agent (Thea & Drew).
**Started:** Oct 14, 2026. **Last updated:** Oct 14, 2026. Rules are numbered in the order
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
- **Not checked:** the page in a published artifact (blocked by TD-20).
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
- **Not checked:** a real rate-limit error from the `sample` capability (blocked by TD-20),
  so the service's actual wording is not known.
- **Main page:** unchanged, `index.html`, 655236 bytes, SHA-256
  `1edc5cca1fbdb8a3462dd647e8711d1ae7634b952915a36abe339a692737d0e2` (as after R2).
- **Enforced by:** `callClaude` and `rateLimitCopy` in `index.html`.

---

## Open items

1. `README.md`, `PUBLISHING.md`, an older changelog entry and the git history still name
   the authors. Decide whether the submission needs them removed too (same as Casting
   Call open item 37).
2. A failed request leaves the reader's message in the conversation. `callClaude` adds it
   before the request and does not remove it on an error, so it goes out with the next
   request. The fallback line "Your message was not sent" is then only half true, and a
   reader who retypes the message sends it twice. Found while checking TD-08; not in its
   scope. To be raised as its own tracker item.

## History

| Date | Rule | Change | Tracker |
| --- | --- | --- | --- |
| Oct 14, 2026 | R1 | Authors' names and emails removed from the consent notice | TD-21 |
| Oct 14, 2026 | R2 | Try-another-browser note added under the sign-in notice | TD-10 |
| Oct 14, 2026 | R3 | Recorded that the rate-limit handling already meets TD-08 (`823b3b1`); no page change | TD-08 |

## Considered, not built

- Replacing the contact line with a generic address. There is no team address, and the
  task asks only for removal.
- Adding the browser advice to the upload field's "Couldn't read" error as well. That is
  where a reader meets the problem, but the task places the note on the consent screen.
