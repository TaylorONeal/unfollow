# X Following Cleanup — @YOUR_HANDLE

Working queue and log for trimming the following list. A daily scheduled task
reads this file, processes a batch, and appends to the log below. This file is
the source of truth: rules, protected contacts, pending reviews, and history
all live here, not in the agent's memory.

- Starting following count: FILL_IN (confirm on setup day)
- Daily cut cap: 50
- Account: https://x.com/YOUR_HANDLE/following
- Setup completed: FILL_IN_DATE

## How this works

X rate-limits the following list hard. Heavy scraping gets the endpoint
throttled to empty within a session. So this runs as a slow daily drip, not a
one-shot purge. Each run loads whatever the list will give that day, screens it
against the protected contacts below, works from the top, cuts up to the cap in
high-confidence matches, and stops.

Inactivity cannot be read from the list itself. The only way to know an
account's last post date is to open its profile. So the daily task opens each
cut-candidate's profile before unfollowing.

## Protected contacts (NEVER cut, never even propose)

<!-- Filled during setup from X DMs, Gmail, Contacts, and manual entries.
     One line per entry, with the source. Add lines yourself any time. -->

### Confirmed handles

- @HANDLE — source (e.g. DM thread / manual / confirmed match)

### Name match-list (from Gmail / Contacts)

<!-- These are names, not handles. During bucketing, any followed account whose
     display name or handle closely matches a name here is protected. Exact
     matches keep silently; fuzzy matches go to "Needs your eyes". -->

- FULL NAME — source (e.g. Gmail sent-mail)

## Keep rules (never auto-unfollow)

<!-- Replace with your real interest lanes, confirmed during setup. Examples: -->
- CATEGORY_1 (e.g. photography: gear brands, photographers you learn from)
- CATEGORY_2 (e.g. local music venues and acts)
- CATEGORY_3 (e.g. tools and companies tied to your work)
- Real people you know or follow intentionally
- Mutuals (accounts that follow you back) — review at most, never auto-cut
- Anything the run is unsure about goes to "Needs your eyes" below, NOT cut

<!-- Protective notes learned from wrong cuts get appended here over time. -->

## Cut rules (unfollow if account matches any, after profile check)

1. Inactive: no posts in 6+ months (checked per profile)
2. Off-interest brands: any brand/company account NOT in the keep categories above
3. Crypto / token shills: $ticker accounts, coin promoters, airdrop/pump spam
4. Engagement / growth farmers: follow-back farms, ebook/link-in-bio funnels
5. Dead / ghost: suspended, deactivated, no avatar + no bio + no posts
6. Don't-recognize / random: no clear tie to the keep interests above

<!-- Custom rules added during setup go here. -->

## Safety

- Never unfollow more than the cap in one run.
- Never touch anything in "Protected contacts".
- If X shows an action-block or the list returns empty, stop for the day and log it.
- When uncertain, do not cut. Log to "Needs your eyes".

---

## Needs your eyes

_(borderline accounts land here; reply with handles to cut and the next run
actions them — anything you don't answer just stays here, it is never cut by
default)_

---

## Run log

| Date | Loaded | Cut | Kept | To review | Notes |
|------|--------|-----|------|-----------|-------|

## Cut history (handle — reason — date)

## Re-follow / mistakes

_(if you flag a wrong cut, note it here so the task learns to protect that
type; if it was a real person, consider adding more protected-contact sources)_
