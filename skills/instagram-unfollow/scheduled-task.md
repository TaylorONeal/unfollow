# Scheduling the daily run

`SKILL.md` says to "pair with a daily scheduled task" — this file is that task.
It contains the exact prompt to schedule, how to schedule it in each Claude
surface, and how to pick a cadence.

## The scheduled prompt

Copy this, fill in the two placeholders, and use it as the body of the scheduled
task. It deliberately repeats the hard rules so the run stays safe even if the
skill file ever fails to load:

```
Run the instagram-unfollow skill's daily run procedure against my queue file at
QUEUE_FILE_PATH for the account @YOUR_HANDLE.

Hard rules, non-negotiable:
- Read the queue file first; it is the source of truth. Action any answered
  "Needs your eyes" items before loading new accounts.
- Never touch any account in the "Protected contacts" section or on my Close
  Friends list.
- Cut at most the daily cap in the queue file, and no more than ~10 in any
  rolling hour, each spaced ~30-60s apart. Respect the warm-up tier.
- Prefer Instagram's "Least interacted with" list (Following → Categories) as the
  candidate pool; fall back to the plain following list if it isn't exposed.
- Open every cut candidate's profile before unfollowing it. Never cut a PRIVATE
  account on inactivity — its posts can't be verified.
- Anything uncertain goes to "Needs your eyes", never cut.
- If Instagram shows an action block, "Try Again Later", "We restrict certain
  activity", a challenge/checkpoint, a login prompt, or the list stops loading:
  stop immediately, log it in the run log, and end the run. Never solve a
  challenge or enter my credentials.
- Log every decision in the queue file (run-log row, cut-history lines, new
  review items) before finishing.

Finish with a 2-3 sentence report: how many cut, what's waiting for my review,
and whether Instagram throttled the run.
```

## How to schedule it

**Claude (Cowork / claude.ai scheduled tasks).** Create a scheduled task with the
prompt above, daily cadence. Make sure the session the task runs in can reach the
queue file (same project/folder) and has browser access to a logged-in
instagram.com session.

**Claude Code (interactive).** Ask Claude Code to "schedule this daily" and it
will create a cron-style scheduled task (CronCreate) with the prompt above. Pick a
time you're normally logged in to Instagram in the automated browser profile.

**Plain OS cron / launchd (headless Claude Code).** Something like:

```cron
# every day at 09:15
15 9 * * * claude -p "$(cat /path/to/skills/instagram-unfollow/scheduled-task-prompt.txt)" --permission-mode acceptEdits
```

where the txt file holds the filled-in prompt above. Headless runs need a
persistent browser profile that stays logged in to Instagram; if login state is
flaky, prefer running it inside an interactive session instead.

## Cadence and timing

- **Daily, low, and paced is the sweet spot.** Instagram cares about per-hour
  volume, so a small daily cap spread across the run beats a big burst. More than
  once a day, or a tight burst, reads as a bot and risks an action block.
- Keep the cap low. Instagram's safe ceiling is roughly 50-60/day for a well-aged
  account and far lower for new ones — the default 30 leaves headroom on purpose.
  New/reactivated/previously-blocked accounts start at 10/day (warm-up tier).
- Pick a consistent-ish human hour (morning coffee time works). Avoid exact
  midnight-style times.
- Expected pace: with a cap of 30 and Instagram's throttling, a 2,000-account
  list that's ~40% cuttable takes roughly 4-6 weeks of daily runs. Low or zero
  cut counts on some days are normal — either Instagram throttled the list or that
  stretch of follows was intentional.
- If two consecutive runs log throttling or action blocks, drop the cap a tier
  (30 → 20 → 10) and/or skip a day before resuming.

## What the scheduled run must NOT do

- Run the setup pass again, re-interview you, or edit the keep/cut rules.
- Cut anything from "Needs your eyes" that you haven't explicitly answered.
- Exceed the daily cap or the hourly pace, or burst-unfollow.
- Retry after a throttle, action block, or challenge within the same day, or
  attempt to solve a challenge/captcha.
