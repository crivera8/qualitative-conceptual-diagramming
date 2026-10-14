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

---

## Open items

1. `README.md`, `PUBLISHING.md`, an older changelog entry and the git history still name
   the authors. Decide whether the submission needs them removed too (same as Casting
   Call open item 37).

## History

| Date | Rule | Change | Tracker |
| --- | --- | --- | --- |
| Oct 14, 2026 | R1 | Authors' names and emails removed from the consent notice | TD-21 |

## Considered, not built

- Replacing the contact line with a generic address. There is no team address, and the
  task asks only for removal.
