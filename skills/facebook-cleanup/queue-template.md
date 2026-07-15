# Facebook Cleanup — YOUR_ACCOUNT

Working queue and log for the `facebook-cleanup` skill. This file is the source
of truth: config, protect-list, keep/cut rules, pending decisions, and full
history all live here, not in the agent's memory. A recurring scheduled task
reads this file, works one surface's batch, and appends to the logs below.

Edit it freely: change the cap, veto items, approve unfriend candidates, add
keep notes. Anything left `pending` is never acted on.

Status vocabulary for "Your call" columns:
`pending` (untouched, skill will not act) | `keep` | `cut` (approved, actioned
next run) | `cut (queued)` (approved, awaiting execution) | `cut (done)`
(executed, dated in notes).

## Config

- Account: YOUR_ACCOUNT
- Daily cap: 50 actions total (groups + pages + people combined)
- People-unfollow policy: FILL_IN (free / propose-first) — set during setup
- Unfriend: NEVER without explicit sign-off (see sign-off section)
- Admin/moderated groups: NEVER auto-left — review lane only
- Protect-list sources: FILL_IN (Messenger recent threads / Gmail / Google
  Contacts / close-friends list / pasted list)
- Stop triggers: any action block, rate warning, checkpoint, captcha, or
  logged-out session
- Setup completed: FILL_IN_DATE
- Last protect-list refresh: never

## Protect-list (never unfollow, never unfriend)

<!-- Filled during setup from Messenger, and Gmail/Contacts if connected, plus
     manual entries. Add lines yourself any time. -->

### Confirmed people

- NAME — source (e.g. Messenger thread / manual / confirmed match)

### Name match-list (from Gmail / Contacts)

<!-- Names, not profiles. During bucketing, any person whose name closely
     matches a name here is protected. Exact matches keep silently; fuzzy
     matches go to "Needs your eyes". -->

- FULL NAME — source (e.g. Gmail sent-mail)

## Keep lanes (never cut)

<!-- Replace with your real interest categories, confirmed during setup. -->
- CATEGORY_1 (your real interest lane)
- CATEGORY_2 (your real interest lane)
- CATEGORY_3 (your real interest lane)
- Real people you know
- Admin/moderated groups — review lane only, never auto-left
- Anything the run is unsure about → "Needs your eyes", never cut

<!-- Era-rule notes: which past cities/schools/chapters are closed, and any
     nuances like "alumni groups: general or current-city only". -->
<!-- Protective notes learned from wrong actions get appended here over time. -->

## Cut rules (act only after verifying)

**Groups — Leave if:** marketplace/buy-sell you never post in; past-event
groups; dead groups (12+ months silent); off-interest with no keep tie; closed
life-era logistics groups (see era notes above); added-without-asking and never
engaged.

**Pages — Unlike/Unfollow if:** off-keep brand/company Pages; defunct Pages;
one-time-reason likes (contest/download/discount); off-interest local/noise.

**People — Unfollow if (only if you opted into free unfollow):** feed presence
you don't want AND not on the protect-list.

**People — Unfriend candidate (sign-off only, never auto):** no recognizable
tie; fake/spam/dormant profile; anything borderline.

<!-- Custom rules added during setup go here. -->

## Safety

- Never act on more than the cap in one run.
- Never touch anything in the protect-list.
- Never unfriend without explicit sign-off below.
- Never auto-leave an admin/moderated group.
- Verify every confirm dialog names the right item before clicking confirm.
- On any action block, checkpoint, captcha, or empty/blank surface: stop for
  the day and log it.
- When uncertain, do not act. Log to "Needs your eyes".

---

## Needs your sign-off (unfriend candidates + admin-group leaves — NOT actioned)

_(reply by editing "Your call" to `cut`; anything left `pending` is never
actioned)_

| Name | Why flagged | Mutuals / context | Proposed | Your call |
|------|-------------|-------------------|----------|-----------|
| | | | | pending |

## Needs your eyes (uncertain — not actioned)

_(borderline items land here; edit "Your call" to `keep` or `cut` — anything
you don't answer just stays, it is never acted on by default)_

| Name | Surface | Why uncertain | Your call |
|------|---------|---------------|-----------|
| | | | pending |

## Run log

| Date | Surface | Loaded | Acted | Kept | To review | Notes |
|------|---------|--------|-------|------|-----------|-------|
| | | | | | | |

## Action history (what was done)

| Date | Name | Surface | Action | Reason |
|------|------|---------|--------|--------|
| | | | | |

## Mistakes / re-add (learning loop)

_(if you flag a wrong action, note it here so the skill learns to protect that
type; if it hit a real person, consider adding more protect-list sources)_

| Date | Name | What happened | Fix applied to keep rules |
|------|------|---------------|---------------------------|
| | | | |
