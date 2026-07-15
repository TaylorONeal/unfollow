---
name: facebook-cleanup
description: >
  Safely declutter a Facebook account with a slow, capped drip. Trims three
  surfaces — Groups (Leave), Pages (Unlike/Unfollow), and People — each with its
  own reversibility. For people it separates Unfollow (stay friends, hide feed —
  reversible) from Unfriend (sever — high risk, always gated behind explicit user
  approval). Builds a protected-contacts list from Messenger, and (if connected)
  Gmail and Google Contacts, so key people are never touched. Buckets every item
  against user-defined keep and cut rules, verifies before acting, verifies every
  confirm dialog names the right item, caps actions per run, and logs everything
  to a local queue file. Uncertain items go to a review list, never acted on;
  admin/moderated groups go to a sign-off list, never auto-left. Supports a
  batch-authorization workflow (census file + one bulk yes from the user) for
  high-throughput cleanup days. Interviews the user in a setup pass to collect
  interests, keep categories, protect-list sources, people policy, and daily cap
  before anything is touched. Trigger on "set up facebook cleanup", "clean up my
  facebook", "trim my groups and pages", "leave old facebook groups", or any
  variant of bulk Facebook cleanup.
---

# Facebook Cleanup

Declutter a bloated Facebook account without severing relationships you care
about. This is a drip system, not a purge: a small capped batch each run,
verified before acting, with a human review lane for anything borderline and a
hard gate on unfriending.

The UI hazards and era rules in this file are field-tested across real cleanup
runs, not theoretical. Facebook's grid is drift-prone and Facebook flags fast
automated behavior; the guardrails here exist because each one caught a real
failure.

## Why a drip

Facebook flags fast automated actions with checkpoints, "you're going too fast"
warnings, and captchas. Slow interaction, working from whatever loads, and
stopping at a daily cap is what survives repeated runs. Facebook also hides
useful signal (last activity, why you joined, admin role) off the list view, so
items must be opened or menu-checked to verify.

## The three surfaces and their actions

Facebook is not one list. Each surface has its own action and its own
reversibility. Getting this distinction right is the whole point of the skill:

1. **Groups → Leave group.** Reversible (can rejoin; private groups need a
   re-invite or re-approval — flag those inline when leaving). Medium risk.
2. **Pages → Unlike / Unfollow Page.** Fully reversible. Low risk. Safest
   surface.
3. **People → two different actions:**
   - **Unfollow**: stay friends, stop seeing their posts. Fully reversible.
     Low risk.
   - **Unfriend**: remove the friendship. Effectively irreversible at scale.
     High risk.

Never conflate Unfollow and Unfriend. Unfollow is a feed-hygiene action.
Unfriend changes a relationship and must always be gated behind explicit user
approval.

## Hard rules (not configurable)

1. Never take more than the daily cap in one run. Default cap: 50 actions total
   across all surfaces. User may lower it.
2. **Never unfriend without the user's explicit approval.** Unfriend candidates
   always go to a sign-off list and wait for a clear yes. Unfollow may be
   allowed freely if the user opts in during setup.
3. **Protect the user's close contacts.** Before touching people, build a
   protect-list from the sources the user named in setup (Messenger threads, a
   named list, Gmail/Contacts if connected). A protected person is never
   unfollowed and never proposed for unfriend.
4. **Never auto-leave a group the user admins or moderates.** Leaving as last
   admin can orphan the group. Detection: the group's "..." menu shows admin
   options like "View member requests", or the group appears under "Groups you
   manage". These always go to the review lane for an explicit go-ahead, even
   if they match every cut rule.
5. Never act on an item matching a keep category. When in doubt, do not act.
6. Every candidate is verified before acting, and every leave/unlike confirm
   dialog is screenshot-checked to confirm it names the RIGHT item before
   clicking confirm. On a name mismatch: cancel, log it, re-locate the correct
   row.
7. Uncertain items go to "Needs your eyes" in the queue file, never acted on.
8. If Facebook shows an action block, a rate warning, a checkpoint, or a
   captcha: stop immediately for the day and log it. Never attempt a captcha.
9. If the session is logged out, stop. The assistant never enters credentials —
   the user logs in themselves, then the run resumes.
10. If anything looks wrong (wrong account, unexpected mass action), stop and
    report.

## Setup pass (run once, before touching anything)

Do NOT act on anything during setup. Collect from the user, in order:

1. **Account** and confirmation it is the right one.
2. **Keep categories**: the user's real interest lanes and the kinds of
   groups/Pages/people they want to protect. Real people they know are always a
   keep. If helpful, load a first batch read-only and propose candidate lanes
   from what you actually see, then let the user confirm and edit — the same
   interview approach the sibling skills use, rather than asking cold.
3. **Cut categories**: confirm which default cut rules below apply, plus any
   extras.
4. **People policy**: may the skill unfollow people freely (feed hygiene), or
   should every unfollow also be proposed first? Unfriend is always sign-off,
   regardless.
5. **Protect-list sources**: where to draw the never-touch people from. Always
   offer Messenger recent threads (strongest in-platform signal — anyone the
   user messages is protected). Offer Gmail sent-mail and Google Contacts if
   those connections exist: names become a match-list, exact matches protect
   silently, fuzzy matches get flagged to review rather than guessed at. Offer
   a manually pasted list (family, close friends, colleagues, clients). Only
   read a source after the user opts into it, read only names/handles (not
   message content), and never write message content into the queue file.
6. **Daily cap**: default 50, user can lower it.
7. **Queue file location**: a local markdown file the user can open and edit.

Then create the queue file from `queue-template.md`, fill in the user's
categories and protect-list, and run one propose-only batch: load a batch,
bucket it, show proposed actions, and let the user veto before the first live
run.

## Default cut rules (act only after verifying)

**Groups — Leave if:**
- Buy/sell/marketplace groups the user never posts in.
- Event groups for events already past.
- Dead groups (no posts in 12+ months, or defunct).
- Off-interest groups with no keep-lane tie and no participation.
- Groups tied to a life era that is over, when the user has confirmed the era
  is over (past home city, old job's product groups, defunct programs). See the
  era rule below.
- Groups the user was added to without asking and never engaged with.

**Pages — Unlike/Unfollow if:**
- Brand/company Pages not in a keep lane.
- Defunct Pages (no posts in 12+ months, or unpublished).
- Pages liked for a one-time reason (contest, download, discount).
- Off-interest local businesses, off-interest noise.

**People — Unfollow if (only if user opted into free unfollow):**
- Feed presence the user doesn't want, as long as the person is NOT on the
  protect-list. Unfollow keeps the friendship, so it is safe across
  acquaintances and dormant links.

**People — Unfriend candidate (queue for sign-off, never auto) if:**
- No recognizable tie, no mutual context, stale-looking add.
- Fake/spam/dormant-looking profile.
- Anything borderline. Borderline always means queue, never act.

## Default keep rules (never auto-act)

- Anything matching a user keep category.
- Real people the user knows. Never an auto-unfriend; only ever a sign-off item.
- Anyone on the protect-list, or any person whose name matches the contact
  match-list (exact match → keep; fuzzy match → review, never act).
- Admin/moderated groups (review lane only).
- Anything the run is not confident about.

## The era rule (learned the hard way)

Do not blanket-cut a city, school, or life chapter. A past era usually contains
two different kinds of groups:

- **Relationship groups** (classmates, cohort groups, family-name groups,
  alumni of the user's actual school): default KEEP — these are real people.
- **Leisure/logistics groups** (that city's venues, restaurants, one-off
  festivals, moving/relocation groups, marketplace groups): cuttable once the
  era is confirmed over.

Alumni chapters get one more filter: the user's own school in general/national
form or in their CURRENT city is a keep signal; chapters for cities they left,
or schools they never attended, are cuts. Confirm the pattern with the user
once, then apply it.

## Rule-conflict protocol

Written rules drift out of date as the user makes calls mid-run. When a written
keep lane conflicts with a newer instruction from the user (e.g. the file says
"lane X = never cut" but last week they said "cut all lane X"), do NOT resolve
it yourself:

1. Queue the affected items to "Needs your eyes" with the conflict spelled out.
2. Ask the user for a ruling.
3. Encode the ruling back into the keep lanes so the conflict cannot recur.

A ruling is often nuanced ("stale ones can go, current ones stay") — capture
the nuance, not just the verdict.

## Two operating modes

**Drip mode (default).** Each run works one surface's rendered batch
autonomously within the cap. Steady, safe, slow. Expect single-digit actions
per run once the obvious junk is gone.

**Batch-authorization mode (the throughput unlock).** Facebook's list views
hard-cap what they render, but the sort selector is per-view. To build a
census: cycle every sort order (Earliest joined, Alphabetical, Most visited,
Recently joined, Default) and collect names from each view. Write the result
into a to-do file bucketed as CUT candidates / REVIEW / KEEP, and hand it to
the user. One bulk reply ("remove everything except A, B, C") authorizes a big
pass that would take weeks of drip. All hard rules still apply during
execution: per-item dialog verification, admin-group holds, caps, and stop
triggers.

## Run procedure (each session)

1. **Read the queue file.** Source of truth for keep/cut rules, cap,
   protect-list, history, and pending review/sign-off items. Action any
   user-approved items first (status `cut (queued)` or an edited "Your call"
   column) — they count against the cap. Skip anything still `pending`.
2. **Refresh the protect-list** from the user's named sources if older than a
   week. Log "unchanged" explicitly when nothing moved.
3. **Pick a surface** (or rotate across runs). Suggested order: Pages (safest),
   then Groups, then People-unfollow. If a surface renders blank or dead (it
   happens — see hazards), log it and fall back to the next surface rather than
   fighting it.
4. **Load a batch gently.** For groups, apply Sort → "Earliest joined first" —
   it surfaces the oldest, stalest groups at the top and dramatically raises
   cut yield. A few scrolls with ~1s pauses. Work with what renders.
5. **Bucket each item**: clear keep (skip), clear cut (candidate), uncertain
   (append to "Needs your eyes"). Route people to unfollow-candidate vs
   unfriend-candidate correctly. Check each group's "..." menu for admin
   signals before treating it as a plain-member cut.
6. **Verify each candidate.** Confirm it matches a cut rule and shows no keep
   tie. If a keep tie appears, move to keep and skip. A quick platform search of
   an ambiguous Page/group name (read-only) is often enough to classify it.
7. **Act on confirmed cuts**, up to the cap, at a human pace, ONE AT A TIME:
   - Open the item's "..." menu, choose Leave/Unfollow.
   - Screenshot the confirm dialog. Verify it names the right item. Cancel on
     mismatch.
   - Admin-leave dialogs have a "prevent re-invite" toggle — leave it OFF
     unless the user said otherwise.
   - After confirming, close any "report this group?" prompt via the X. Never
     report.
   - Expect a blank or reflowed frame for a few seconds after each action. Wait
     for the page to settle and re-screenshot before the next click. Never
     scroll or click while a dialog might be open.
8. **Log everything** in the queue file: run-log row, one action-history line
   per action, new review/sign-off items. Also sweep the review tables for rows
   that were actually resolved in earlier runs and mark them done — stale
   `pending` rows cause repeat work.
9. **Report**: 2-3 sentences. What was cleaned, what awaits sign-off, throttle
   status, rough estimate remaining.

## Known UI hazards (all observed in the field)

- **The groups joins page (/groups/joins) hard-caps around 20 rendered cards
  per sort view** and refuses to paginate, in every sort order. Work the batch
  that loads; use the multi-sort census (batch-authorization mode) to see more
  of the long tail.
- **Sort → "Earliest joined first" is the highest-yield groups view.** Oldest
  groups are the stalest, and staying at the top of the list avoids scroll
  drift.
- **The profile /following grid often renders only ~8 rows** before stalling on
  skeletons. Same rule: work what loads, let the schedule chip at the rest.
- **The Pages likes view can die entirely** (renders a blank content area for
  whole sessions). Log it, fall back to another surface, retry next run.
- **The grid is click-drift-prone and reflows after every action.** One item at
  a time, dialog-name verification before every confirm, no scrolling with a
  dialog possibly open, and a settle-pause after each action. Stray confirms
  WILL leave the wrong group otherwise — dialog verification has caught
  wrong-row mismatches in practice.
- **Post-leave prompts**: "report this group?" appears after many leaves —
  always close, never report. Left items may keep their card in the stale grid
  (showing "Answer questions" or similar rejoin state) — do not re-click them;
  confirm success via the joined-count or the button state.
- **Session logouts happen mid-run.** Chrome extensions reconnect, Facebook
  sometimes comes back logged out. Stop, ask the user to log in, resume. Never
  touch the credential form even if the browser pre-filled it.

## Learning loop

- If the user flags a wrong action, record it under "Mistakes / re-add" and add
  a protective note to the keep rules so that type is never acted on again.
- When the user issues a mid-run ruling (an exception, an expansion, a new
  nuance), write it into the keep/cut rules immediately — mid-run rulings are
  the highest-value training data this skill gets.
- If a wrong action hit a real person, ask whether to expand the protect-list
  sources (e.g. connect Gmail/Contacts if they were not connected at setup).
- Dense stretches of keeps mean low action counts. That is correct, not a
  failure.

## Scheduling

Pair with a recurring scheduled task (weekly is a sane Facebook default).
`scheduled-task.md` in this folder contains the exact prompt to schedule, the
cadence recommendation, and setup instructions. The scheduled prompt must
reference the queue file path and repeat the hard rules: the cap, never unfriend
without sign-off, protect the protect-list, admin-group holds, and stop on any
checkpoint/captcha.

## Customizing this skill

This ships generic and gets customized per user during the setup pass — the
setup pass writes the user's real keep lanes, protect-list, and people policy
into their queue file. To hard-fork a permanent personal version instead: copy
this folder, rename the skill, and bake the user's keep lanes and protect-list
sources directly into this `SKILL.md` and the queue file so no setup pass is
needed. See the root `README.md` for the full walkthrough.
