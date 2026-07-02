---
name: x-unfollow
description: >
  Safely trim an X (Twitter) following list with a slow daily drip. Interviews the
  user to build keep categories, builds a protected-contacts list from Gmail,
  Google Contacts, and X DMs so key people are never cut, buckets each followed
  account against keep and cut rules, verifies every cut candidate's profile
  before unfollowing, caps cuts per run, and logs everything to a local queue
  file. Uncertain accounts are never cut; they go to a "Needs your eyes" list for
  the user to decide. Trigger on "set up x unfollow", "clean up my twitter
  following", "trim who I follow on X", "unfollow inactive accounts", or any
  variant of bulk following cleanup on X/Twitter.
---

# X Following Cleanup

Trim a bloated X following list without nuking accounts the user actually cares
about. This is a drip system, not a purge: a small capped batch each run,
profile-verified cuts only, a protected-contacts list that is never touched, and
a human review lane for everything borderline.

## Why a drip

X rate-limits the `/following` endpoint hard. Aggressive scraping gets the list
throttled to empty within a session. Slow scrolling with pauses, working from
whatever loads, and stopping at a daily cap is the only approach that survives
repeated runs. Inactivity also cannot be read from the list itself; the only way
to know an account's last post date is to open its profile.

## Hard rules (not configurable)

1. Never unfollow more than the daily cap in one run. Default cap: 50.
2. Never cut an account on the protected-contacts list. Not even to propose it.
3. Never cut an account matching a keep category. When in doubt, do not cut.
4. Every cut candidate gets its profile opened and checked before unfollowing.
5. Uncertain accounts go to "Needs your eyes" in the queue file, never cut.
6. If X shows an action block, a rate-limit message, or the list renders empty:
   stop immediately for the day and log it.
7. If anything looks wrong (wrong account, unexpected mass-cut, login issue),
   stop and report rather than pushing through.
8. Unfollows are effectively irreversible at scale (no bulk re-follow), so bias
   every decision toward keeping.
9. Never unfollow anything during the setup pass.

## Setup pass (run once, before any unfollow)

Do NOT unfollow anything during setup. The setup pass has five steps, in order.

### Step 1 — Account

Collect the user's handle and confirm it is the right account (open
`x.com/HANDLE` and confirm the display name with the user). Note the starting
following count.

### Step 2 — Keep categories (interactive)

Do not just ask "what are your interests" cold. Instead:

1. Open the following list and gently load a first sample (a few scrolls,
   ~1s pauses, ~50-100 rows).
2. From the sample, infer 4-8 candidate interest lanes (for example:
   photography, climbing, fintech, local music, AI/dev tools) and present them
   to the user as a menu: "Here's what your following list looks like it's
   made of — which of these are keepers?" Use a structured question tool
   (AskUserQuestion or equivalent) if available, with multi-select.
3. Let the user confirm, edit, remove, and add lanes the sample missed.
4. For each confirmed lane, ask one clarifying follow-up if the lane is broad
   (e.g. "photography — gear brands and pros too, or just people you know?").

Real people the user knows are always a keep regardless of lane.

### Step 3 — Protected contacts (never-cut list)

This is the safety net that keeps the drip from ever cutting someone who
matters. Build it from whichever sources are available, and always offer all of
them — the user picks which to use:

**a. X DMs (browser, always available if logged in).** Open `x.com/messages`
and collect the handle of every conversation partner, scrolling gently. Anyone
the user has ever DM'd is protected automatically — a DM thread is the
strongest in-platform signal of a real relationship.

**b. Gmail (if a Gmail tool/MCP connection is available).** Search sent mail
for frequent correspondents (people the user has actually written to, not
newsletters). Collect real names and email handles. These do not map 1:1 to X
handles, so treat them as a *match list*: during bucketing, if a followed
account's display name or handle closely matches a Gmail correspondent, it is
protected. Exact/obvious matches are protected silently; fuzzy matches go to
"Needs your eyes" as "possible contact match — confirm" rather than being
silently protected or cut. If no Gmail connection is available, offer to
proceed without it and say what the user would gain by connecting it.

**c. Google Contacts (if available).** Same treatment as Gmail: names become a
match list, exact matches protect, fuzzy matches get flagged.

**d. Manual.** Ask the user to paste any handles that must never be touched:
family, close friends, colleagues, clients, their own alt accounts.

Write every protected handle (and the name match-list) into the "Protected
contacts" section of the queue file with its source, e.g.
`@janedoe — DM thread` or `Jane Doe — Gmail sent-mail match list`. Ask the
user to skim the list once before the first live run.

Consent note: only read DMs/Gmail/Contacts after the user explicitly opts in
to each source, read only what is needed to build the list (senders/recipients
and names, not message content), and never write any message content into the
queue file.

### Step 4 — Cut rules and cap

1. Walk through the default cut rules below; confirm which apply and collect
   any extras (e.g. "also cut anything political" or "keep all news orgs").
2. Daily cap: default 50, user can lower it. Suggest 25 for accounts under
   1,000 following or if the user is nervous.

### Step 5 — Queue file and dry run

1. Create the queue file from `queue-template.md`, filling in the handle,
   count, cap, keep categories, protected contacts, and any custom cut rules.
   Confirm where it lives (a local markdown file the user can open and edit).
2. Run one sample batch in **propose-only mode**: load a batch, bucket it,
   show the user the proposed cuts with one-line reasons, and let them veto.
   Nothing is unfollowed. Wrong proposals become keep-rule notes.
3. Offer to set up the daily scheduled run (see `scheduled-task.md`).

## Default cut rules (unfollow if account matches any, after profile check)

1. Inactive: no posts in 6+ months (checked on the profile, not the list).
2. Off-interest brands: brand/company accounts not in any keep category.
3. Crypto/token shills: $ticker accounts, coin promoters, airdrop/pump spam.
4. Engagement/growth farmers: follow-back farms, "hit that follow button" bios,
   ebook/course/consultation link-in-bio funnels, X-growth-tool promoters.
5. Dead/ghost: suspended, deactivated, or no avatar + no bio + no posts.
6. Unrecognized randoms: no clear tie to any keep interest, likely a reflexive
   follow. Use this rule cautiously; real people default to "Needs your eyes".

## Default keep rules (never auto-unfollow)

- Any handle in the protected-contacts list, or any account whose name matches
  the contact match-list (exact match → keep; fuzzy match → review, never cut).
- Any account matching a user keep category.
- Real people the user knows or plausibly follows intentionally, even
  off-interest. Notable/active real people are a review item, never an auto-cut.
- Mutuals (accounts that follow the user back) are never auto-cut; they go to
  review at most.
- Anything the run is not confident about.

## Daily run procedure

1. **Read the queue file.** It is the source of truth for rules, cap, protected
   contacts, history, and pending review items. If the user has answered review
   items, action those decisions first (they count against the cap).
2. **Open the following list** via browser automation
   (`x.com/HANDLE/following`). If not logged in or the list renders empty, stop
   and log "not logged in / throttled". Do not retry aggressively.
3. **Load a batch gently.** A few scrolls with ~1s pauses, collecting handle,
   name, and bio per row. No long tight scroll loops. If the list stops
   growing, work with what loaded.
4. **Screen against protected contacts first.** Any row matching the protected
   list or the contact match-list is a keep before any other bucketing.
5. **Bucket each remaining account**: clear keep (skip), clear cut (candidate),
   uncertain (append to "Needs your eyes").
6. **Verify each cut candidate** by opening its profile. Check last post date
   and whether the bio/content actually matches a cut rule. If the profile
   reveals a keep-category tie, a mutual follow, or a DM history indicator,
   move it to keep and skip.
7. **Unfollow confirmed cuts**, up to the cap, at a human pace: click
   Following, confirm the Unfollow dialog, verify the button flipped to Follow.
   On any action block or unresponsive button, stop for the day.
8. **Log everything** in the queue file: a run-log row (date, loaded, cut,
   kept, to-review, notes), one cut-history line per unfollow (handle, reason,
   date), and new review items.
9. **Report**: 2-3 sentences. Cuts, review flags, throttle status, estimated
   follows remaining.

## Learning loop

- If the user flags a wrong cut, record it under "Re-follow / mistakes" in the
  queue file, offer to re-follow it immediately, and add a protective note to
  the keep rules so the same type is never cut again.
- If a wrong cut was a real person, ask whether to expand the protected
  contacts sources (e.g. connect Gmail if it wasn't connected at setup).
- If a whole stretch of the list is dense with intentional follows, expect low
  cut counts. That is correct behavior, not a failure.

## Scheduling

Pair with a daily scheduled task that runs this skill against the queue file.
`scheduled-task.md` in this folder contains the exact prompt to schedule, the
cadence recommendation, and setup instructions for Claude scheduled tasks,
Claude Code cron, and plain OS cron.
