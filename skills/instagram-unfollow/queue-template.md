# Instagram Following Cleanup — @YOUR_HANDLE

Working queue and log for trimming the following list. A daily scheduled task
reads this file, processes a small batch, and appends to the log below. This file
is the source of truth: rules, cap, protected contacts, pending reviews, and
history all live here, not in the agent's memory.

- Starting following count: FILL_IN (confirm on setup day)
- Daily cut cap: 30
- Hourly pace: max ~10 unfollows / rolling hour, ~30-60s apart
- Warm-up tier: FILL_IN (10/day for new/reactivated/previously-blocked accounts;
  step 10 → 15 → 20 → 30 only if no restriction appears)
- Account: https://instagram.com/YOUR_HANDLE/following
- Setup completed: FILL_IN_DATE

## How this works

Instagram action-blocks fast-repeated follow/unfollow activity, and cares about
per-hour pacing as much as the daily total. So this runs as a slow, paced daily
drip, not a one-shot purge. Each run prefers Instagram's own **"Least interacted
with"** list as its candidate pool, screens it against the protected contacts and
Close Friends below, verifies each candidate's profile, cuts up to the cap at a
human rhythm, and stops the instant Instagram shows any restriction message.

Inactivity cannot be read from the following list itself — the task opens each
cut-candidate's profile first. **Private** accounts can't be verified this way and
are never cut on inactivity.

## Protected contacts (NEVER cut, never even propose)

<!-- Filled during setup from Close Friends, Instagram DMs, Gmail, Contacts, and
     manual entries. One line per entry, with the source. Add lines yourself any
     time. -->

### Confirmed handles

- @HANDLE — source (e.g. Close Friends / DM thread / manual / confirmed match)

### Name match-list (from Gmail / Contacts)

<!-- These are names, not handles. During bucketing, any followed account whose
     display name or handle closely matches a name here is protected. Exact
     matches keep silently; fuzzy matches go to "Needs your eyes". -->

- FULL NAME — source (e.g. Gmail sent-mail)

## Keep rules (never auto-unfollow)

<!-- Replace with your real interest lanes, confirmed during setup. Examples: -->
- CATEGORY_1 (e.g. photography: gear brands, photographers you learn from)
- CATEGORY_2 (e.g. local restaurants and food spots you've been to)
- CATEGORY_3 (e.g. a fandom / community you're active in)
- Real people you know or follow intentionally
- Mutuals (accounts that follow you back) — review at most, never auto-cut
- Private accounts you follow — content can't be verified; keep/review, never cut
  on inactivity
- Anything the run is unsure about goes to "Needs your eyes" below, NOT cut

<!-- Protective notes learned from wrong cuts get appended here over time. -->

## Cut rules (unfollow if account matches any, after profile check)

1. Inactive: no posts in 6+ months (checked per profile; PUBLIC accounts only)
2. Off-interest brands: any brand/company account NOT in the keep categories above
3. Spam / bots / engagement farmers: follow-for-follow, giveaway/loop accounts,
   "DM for promo", mass-follow bots, crypto/forex/OF-promo spam, link-in-bio funnels
4. Dead / ghost: deactivated, no avatar + no bio + no posts, impersonator/recycled
5. Don't-recognize / random: no clear tie to the keep interests above
6. Native "Least interacted with" members that also match a rule above

<!-- Custom rules added during setup go here. -->

## Safety

- Never unfollow more than the cap in one run, or more than ~10 in a rolling hour.
- Never touch anything in "Protected contacts" or on your Close Friends list.
- Never auto-cut a private account on inactivity — you can't see its posts.
- If Instagram shows an action block, "Try Again Later", a challenge, or the list
  stops loading: stop for the day and log it. Never solve a challenge.
- When uncertain, do not cut. Log to "Needs your eyes".

---

## Needs your eyes

_(borderline accounts land here; reply with handles to cut and the next run
actions them — anything you don't answer just stays here, it is never cut by
default)_

| Handle | Why uncertain | Your call |
|--------|---------------|-----------|
| | | pending |

---

## Run log

| Date | Loaded | Cut | Kept | To review | Notes (incl. throttle/warm-up tier) |
|------|--------|-----|------|-----------|-------------------------------------|

## Cut history (handle — reason — date)

## Re-follow / mistakes

_(if you flag a wrong cut, note it here so the task learns to protect that type;
if it was a real person, consider adding more protected-contact sources)_
