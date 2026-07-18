---
name: instagram-unfollow
description: >
  Safely trim an Instagram following list with a slow, low-volume daily drip.
  Interviews the user to build keep categories, builds a protected-contacts list
  from Instagram DMs, the native Close Friends list, Gmail, and Google Contacts so
  key people are never cut, reads Instagram's own "Least interacted with" and
  "Most shown in feed" lists as high-signal candidate sources, buckets each
  followed account against keep and cut rules, verifies every cut candidate's
  profile before unfollowing, caps cuts per run far lower than the other platforms
  and paces them within the run, and logs everything to a local queue file.
  Uncertain accounts are never cut; they go to a "Needs your eyes" list for the
  user to decide. Trigger on "set up instagram unfollow", "clean up my instagram
  following", "trim who I follow on instagram/IG", "unfollow inactive accounts on
  instagram", or any variant of bulk following cleanup on Instagram.
---

# Instagram Following Cleanup

Trim a bloated Instagram following list without nuking accounts the user actually
cares about, and without tripping Instagram's action blocks. This is a drip
system, not a purge: a small capped batch each run *paced within the run*,
profile-verified cuts only, a protected-contacts list that is never touched, and
a human review lane for everything borderline.

Instagram is the strictest platform in this skill family. It action-blocks
aggressively and cares about *per-hour* pacing as much as per-day totals, so the
default cap is lower than X's and the run deliberately slows itself down. When a
rule here differs from the sibling `x-unfollow` skill, the Instagram version is
the more conservative one on purpose — follow it.

## Why a low, paced drip

Instagram throttles and action-blocks fast-repeated follow/unfollow actions. The
community-safe ceiling for an aged, well-established account is roughly 50-60
unfollows per day, and far lower for new or lightly-used accounts — with rolling
*hourly* volume mattering as much as the daily total. Rapid, evenly-timed clicks
read as a bot even below the daily number. So this skill:

- caps the day low (default 30) and lets the user go lower;
- never fires more than a small sub-batch in any rolling hour;
- spaces each unfollow with a real, slightly irregular pause;
- stops the instant Instagram shows any restriction message, and does not retry.

Inactivity also cannot be read from the following list itself; the only way to
know an account's last post date is to open its profile — and for **private**
accounts you follow, you cannot see posts at all, so inactivity is unverifiable
and such accounts are never cut on that basis.

## Hard rules (not configurable)

1. Never unfollow more than the daily cap in one run. Default cap: 30.
2. Never exceed the hourly pace: at most ~10 unfollows in any rolling hour, each
   spaced ~30-60s apart with slight variation. A scheduled run that cannot spread
   across time does small clusters with pauses, never a tight burst.
3. Never cut an account on the protected-contacts list, or on the native Close
   Friends list. Not even to propose it.
4. Never cut an account matching a keep category. When in doubt, do not cut.
5. Every cut candidate gets its profile opened and checked before unfollowing.
6. Never auto-cut a **private** account you follow on an inactivity/"can't tell"
   basis — you cannot see its posts to verify. Route to review at most.
7. Uncertain accounts go to "Needs your eyes" in the queue file, never cut.
8. If Instagram shows an action block, "Try Again Later", "We restrict certain
   activity", a challenge/checkpoint, a login prompt, or the list stops loading:
   stop immediately for the day and log it. Never attempt a challenge/captcha.
9. If anything looks wrong (wrong account, unexpected mass-cut, login issue),
   stop and report rather than pushing through.
10. Unfollows are effectively irreversible at scale (no bulk re-follow), so bias
    every decision toward keeping.
11. Never unfollow anything during the setup pass. The assistant never enters the
    user's credentials — the user logs in themselves.

## New-account warm-up

Action limits scale with account age and history. If the account is new, was
recently reactivated, or has a history of prior action blocks, do NOT start at
the default cap. Start at **10/day for the first week**, then step up (10 → 15 →
20 → 30) only if no restriction message appears. An account that has been
action-blocked before stays at the low tier until the user says otherwise.

## Setup pass (run once, before any unfollow)

Do NOT unfollow anything during setup. The setup pass has five steps, in order.

### Step 1 — Account

Collect the user's handle and confirm it is the right account (open
`instagram.com/HANDLE` and confirm the display name/avatar with the user). Note
the starting following count and roughly how old/active the account is (this sets
the warm-up tier above).

### Step 2 — Keep categories (interactive), seeded by Instagram's own lists

Do not just ask "what are your interests" cold. Instead:

1. **Read Instagram's native cleanup lists first.** In the app (and, where
   exposed, mobile web): Profile → Following → **Categories** surfaces **"Least
   interacted with"** and **"Most shown in feed"**. "Least interacted with" is
   the single highest-signal candidate source Instagram gives you — accounts you
   never engage with. Load it read-only and keep it as the first-pass candidate
   pool. (On desktop web this category view is often not exposed; if so, fall
   back to loading the plain following list and note the limitation.)
2. Also gently load a first sample of the plain following list (a few scrolls,
   ~1-2s pauses, ~50-80 rows collected as you scroll — see the DOM note below).
3. From what you see, infer 4-8 candidate interest lanes (for example:
   photography, climbing, food/restaurants, local music, meme/humor accounts, a
   fandom, work-adjacent brands) and present them to the user as a menu: "Here's
   what your following list looks like it's made of — which of these are
   keepers?" Use a structured question tool (AskUserQuestion or equivalent) if
   available, with multi-select.
4. Let the user confirm, edit, remove, and add lanes the sample missed.
5. For each broad lane, ask one clarifying follow-up (e.g. "food — restaurants
   you've actually been to, or aspirational/recipe accounts too?").

Real people the user knows are always a keep regardless of lane.

### Step 3 — Protected contacts (never-cut list)

This is the safety net that keeps the drip from ever cutting someone who matters.
Build it from whichever sources are available, and always offer all of them — the
user picks which to use:

**a. Close Friends list (native, always available if logged in).** Instagram's
green-star Close Friends list is an explicit "these people matter" signal the
user already curated. Read it and protect every account on it automatically.

**b. Instagram DMs (browser, always available if logged in).** Open
`instagram.com/direct/inbox` and collect the handle of every conversation
partner, scrolling gently. Anyone the user has an ongoing DM thread with is
protected automatically — a DM thread is the strongest in-platform signal of a
real relationship. Group-thread members are protected too.

**c. Gmail (if a Gmail tool/MCP connection is available).** Search sent mail for
frequent correspondents (people the user has actually written to, not
newsletters). Collect real names and email handles. These do not map 1:1 to
Instagram handles, so treat them as a *match list*: during bucketing, if a
followed account's display name or handle closely matches a Gmail correspondent,
it is protected. Exact/obvious matches are protected silently; fuzzy matches go
to "Needs your eyes" as "possible contact match — confirm" rather than being
silently protected or cut. If no Gmail connection is available, offer to proceed
without it and say what the user would gain by connecting it.

**d. Google Contacts (if available).** Same treatment as Gmail: names become a
match list, exact matches protect, fuzzy matches get flagged.

**e. Manual.** Ask the user to paste any handles that must never be touched:
family, close friends, partners, colleagues, clients, their own alt accounts.

Write every protected handle (and the name match-list) into the "Protected
contacts" section of the queue file with its source, e.g. `@janedoe — Close
Friends` or `@mike_r — DM thread` or `Jane Doe — Gmail sent-mail match list`. Ask
the user to skim the list once before the first live run.

Consent note: only read Close Friends / DMs / Gmail / Contacts after the user
explicitly opts in to each source, read only what is needed to build the list
(handles and names, not message content), and never write any message content
into the queue file.

### Step 4 — Cut rules, cap, and warm-up tier

1. Walk through the default cut rules below; confirm which apply and collect any
   extras (e.g. "also cut all giveaway/loop accounts" or "keep every local
   business I've visited").
2. Daily cap: default 30, user can lower it. Set the **warm-up tier** (above) if
   the account is new/reactivated/previously-blocked — start at 10/day.
3. Confirm the run may lean on the "Least interacted with" list as its primary
   candidate pool (recommended) versus working the full following list top-down.

### Step 5 — Queue file and dry run

1. Create the queue file from `queue-template.md`, filling in the handle, count,
   cap, warm-up tier, keep categories, protected contacts, and any custom cut
   rules. Confirm where it lives (a local markdown file the user can open and
   edit).
2. Run one sample batch in **propose-only mode**: load a batch (prefer "Least
   interacted with"), bucket it, show the user the proposed cuts with one-line
   reasons, and let them veto. Nothing is unfollowed. Wrong proposals become
   keep-rule notes.
3. Offer to set up the daily scheduled run (see `scheduled-task.md`).

## Default cut rules (unfollow if account matches any, after profile check)

1. Inactive: no posts in 6+ months (checked on the profile, not the list). Public
   accounts only — a **private** account's inactivity cannot be verified, so it
   is never cut on this rule.
2. Off-interest brands: brand/company accounts not in any keep category.
3. Spam / bots / engagement farmers: follow-for-follow and follow-back farms,
   giveaway/"loop" accounts, "DM for promo/collab", mass-follow bots, crypto/
   forex/OF-promo spam, link-in-bio funnel accounts.
4. Dead / ghost: deactivated, no avatar + no bio + no posts, obvious impersonator
   or recycled-username account.
5. Unrecognized randoms: no clear tie to any keep interest, likely a reflexive or
   follow-back follow. Use this rule cautiously; real people default to "Needs
   your eyes".
6. Native "Least interacted with" members that also match one of the rules above
   — the native list raises confidence, it does not replace the profile check or
   the protected-contact screen.

## Default keep rules (never auto-unfollow)

- Any handle in the protected-contacts list, anyone on the Close Friends list, or
  any account whose name matches the contact match-list (exact match → keep;
  fuzzy match → review, never cut).
- Any account matching a user keep category.
- Real people the user knows or plausibly follows intentionally, even
  off-interest. Notable/active real people are a review item, never an auto-cut.
- Mutuals (accounts that follow the user back) are never auto-cut; they go to
  review at most.
- Private accounts the user follows — the user chose to request them and their
  content can't be verified; keep or review, never auto-cut on inactivity.
- Anything the run is not confident about.

## Daily run procedure

1. **Read the queue file.** It is the source of truth for rules, cap, warm-up
   tier, protected contacts, history, and pending review items. If the user has
   answered review items, action those decisions first (they count against the
   cap).
2. **Open the following surface** via browser automation. Prefer the native
   **"Least interacted with"** list (Profile → Following → Categories) if
   available; otherwise open `instagram.com/HANDLE/following`. If not logged in,
   or the list renders empty, or a challenge appears: stop and log "not logged in
   / throttled / challenged". Do not retry aggressively and never solve a
   challenge.
3. **Load a batch gently, collecting as you scroll.** On desktop web the
   following list is a **modal dialog** that *virtualizes* rows — as you scroll
   down, rows that leave the viewport are removed from the DOM, so you must
   capture handle/name/bio *while each row is on screen*, not in one pass at the
   end. A few scrolls with ~1-2s pauses. On mobile web it is a full page but
   still lazy-loads. If the list stops growing, work with what loaded.
4. **Screen against protected contacts + Close Friends first.** Any row matching
   the protected list, the Close Friends list, or the contact match-list is a
   keep before any other bucketing.
5. **Bucket each remaining account**: clear keep (skip), clear cut (candidate),
   uncertain (append to "Needs your eyes"). Send private accounts to review, not
   to cut, unless they match a non-inactivity spam/bot rule outright.
6. **Verify each cut candidate** by opening its profile. Check last post date,
   post count, whether it is private, and whether the bio/content actually
   matches a cut rule. If the profile reveals a keep-category tie, a mutual
   follow, a Close Friends/DM indicator, or that it's a real person, move it to
   keep and skip.
7. **Unfollow confirmed cuts**, up to the cap and within the hourly pace, at a
   human rhythm: click **Following** on the row/profile, confirm the **Unfollow**
   dialog, verify the button flipped to **Follow**. Space each ~30-60s. On any
   action block, "Try Again Later", challenge, or unresponsive button, stop for
   the day immediately.
8. **Log everything** in the queue file: a run-log row (date, loaded, cut, kept,
   to-review, notes incl. any throttle), one cut-history line per unfollow
   (handle, reason, date), and new review items.
9. **Report**: 2-3 sentences. Cuts, review flags, throttle status, estimated
   follows remaining.

## Known UI hazards (Instagram-specific)

- **The following modal recycles DOM rows.** Collect data as rows scroll into
  view; a single end-of-scroll sweep misses everything that already unmounted.
- **"Least interacted with" / "Most shown in feed" live under Following →
  Categories** and are frequently app-/mobile-only. On desktop web, expect to
  fall back to the plain list and note it in the run log.
- **The Unfollow confirm is a two-step dialog.** Clicking Following opens a sheet
  where "Unfollow" is at the bottom (often in red). Confirm you clicked the row's
  own control — the modal reflows after each action and is drift-prone.
- **Action-block messages are the hard stop.** "Try Again Later", "We restrict
  certain activity to protect our community", or a "confirm it's you" challenge
  all mean: stop for the day, log it, drop the next run's cap a tier. Never solve
  a challenge or re-auth on the user's behalf.
- **Sessions log out mid-run.** If the surface returns to a login screen, stop
  and ask the user to log in themselves; never touch the credential form.

## Learning loop

- If the user flags a wrong cut, record it under "Re-follow / mistakes" in the
  queue file, offer to re-follow it immediately, and add a protective note to the
  keep rules so the same type is never cut again.
- If a wrong cut was a real person, ask whether to expand the protected contacts
  sources (e.g. connect Gmail/Contacts, or re-read Close Friends/DMs if a source
  was skipped at setup).
- If two consecutive runs log throttling or action blocks, drop the cap a tier
  (30 → 20 → 10) and/or skip a day before resuming — this is expected Instagram
  behavior, not a failure.
- If a whole stretch of the list is dense with intentional follows, expect low
  cut counts. That is correct behavior.

## Scheduling

Pair with a daily scheduled task that runs this skill against the queue file.
`scheduled-task.md` in this folder contains the exact prompt to schedule, the
cadence recommendation, and setup instructions for Claude scheduled tasks,
Claude Code cron, and plain OS cron.

## Customizing this skill

This ships generic and gets customized per user during the setup pass — the setup
pass writes the user's real keep lanes, protected contacts, warm-up tier, and cap
into their queue file. To hard-fork a permanent personal version instead: copy
this folder, rename the skill, and bake the user's keep lanes and protected
sources directly into this `SKILL.md` and the queue file so no setup pass is
needed. See the root `README.md` for the full walkthrough.
