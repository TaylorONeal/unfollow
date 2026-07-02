# Scheduling the daily run

`SKILL.md` says to "pair with a daily scheduled task" — this file is that task.
It contains the exact prompt to schedule, how to schedule it in each Claude
surface, and how to pick a cadence.

## The scheduled prompt

Copy this, fill in the two placeholders, and use it as the body of the
scheduled task. It deliberately repeats the hard rules so the run stays safe
even if the skill file ever fails to load:

```
Run the x-unfollow skill's daily run procedure against my queue file at
QUEUE_FILE_PATH for the account @YOUR_HANDLE.

Hard rules, non-negotiable:
- Read the queue file first; it is the source of truth. Action any answered
  "Needs your eyes" items before loading new accounts.
- Never touch any account in the "Protected contacts" section.
- Cut at most the daily cap listed in the queue file.
- Open every cut candidate's profile before unfollowing it.
- Anything uncertain goes to "Needs your eyes", never cut.
- If X shows an action block, a rate-limit, a login prompt, or the following
  list renders empty: stop immediately, log it in the run log, and end the run.
- Log every decision in the queue file (run-log row, cut-history lines, new
  review items) before finishing.

Finish with a 2-3 sentence report: how many cut, what's waiting for my review,
and whether X throttled the run.
```

## How to schedule it

**Claude (Cowork / claude.ai scheduled tasks).** Create a scheduled task with
the prompt above, daily cadence. Make sure the session the task runs in can
reach the queue file (same project/folder) and has browser access to a
logged-in x.com session.

**Claude Code (interactive).** Ask Claude Code to "schedule this daily" and it
will create a cron-style scheduled task (CronCreate) with the prompt above.
Pick a time you're normally logged in to X in the automated browser profile.

**Plain OS cron / launchd (headless Claude Code).** Something like:

```cron
# every day at 09:15
15 9 * * * claude -p "$(cat /path/to/skills/x-unfollow/scheduled-task-prompt.txt)" --permission-mode acceptEdits
```

where the txt file holds the filled-in prompt above. Headless runs need a
persistent browser profile that stays logged in to X; if login state is flaky,
prefer running it inside an interactive session instead.

## Cadence and timing

- **Daily is the sweet spot.** More than once a day looks bot-like to X and
  risks action blocks; less often and a big list takes months.
- Pick a consistent-ish human hour (morning coffee time works). Avoid exact
  midnight-style times.
- Expected pace: with a cap of 50 and typical throttling, a 2,000-account list
  with ~40% cuttable takes roughly 3-4 weeks of daily runs.
- If two consecutive runs log throttling or action blocks, drop the cap (50 →
  25) and/or skip a day before resuming.

## What the scheduled run must NOT do

- Run the setup pass again, re-interview you, or edit the keep/cut rules.
- Cut anything from "Needs your eyes" that you haven't explicitly answered.
- Retry after a throttle or action block within the same day.
