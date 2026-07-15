# Scheduling the recurring run

`SKILL.md` says to "pair with a recurring scheduled task" — this file is that
task. It contains the exact prompt to schedule, how to schedule it in each
Claude surface, and how to pick a cadence.

## The scheduled prompt

Copy this, fill in the two placeholders, and use it as the body of the
scheduled task. It deliberately repeats the hard rules so the run stays safe
even if the skill file ever fails to load:

```
Run the facebook-cleanup skill's run procedure against my queue file at
QUEUE_FILE_PATH for the account YOUR_ACCOUNT.

Hard rules, non-negotiable:
- Read the queue file first; it is the source of truth. Action any items I have
  approved (status "cut" / "cut (queued)") before loading anything new. Skip
  anything still "pending".
- Never touch anyone in the "Protect-list" section.
- NEVER unfriend anyone. Unfriend candidates go to the sign-off list and wait
  for my explicit yes.
- NEVER auto-leave a group I admin or moderate. Route it to the review lane.
- Act on at most the daily cap listed in the queue file, across all surfaces
  combined, one item at a time, at a human pace.
- Before clicking any Leave/Unfollow confirm dialog, verify it names the RIGHT
  item. Cancel and re-locate on any mismatch.
- Anything uncertain goes to "Needs your eyes", never actioned.
- If Facebook shows an action block, a rate warning, a checkpoint, a captcha,
  or a logged-out session: stop immediately, log it, and end the run. Never
  attempt a captcha or enter credentials.
- Close any "report this group?" prompt without reporting.
- Log every decision in the queue file (run-log row, action-history lines, new
  review/sign-off items) before finishing.

Finish with a 2-3 sentence report: what was cleaned, what's waiting for my
sign-off or review, and whether Facebook throttled the run.
```

## How to schedule it

**Claude (Cowork / claude.ai scheduled tasks).** Create a scheduled task with
the prompt above, weekly cadence. Make sure the session the task runs in can
reach the queue file (same project/folder) and has browser access to a
logged-in facebook.com session.

**Claude Code (interactive).** Ask Claude Code to "schedule this weekly" and it
will create a cron-style scheduled task (CronCreate) with the prompt above.
Pick a time you're normally logged in to Facebook in the automated browser
profile.

**Plain OS cron / launchd (headless Claude Code).** Something like:

```cron
# every Sunday at 10:15
15 10 * * 0 claude -p "$(cat /path/to/skills/facebook-cleanup/scheduled-task-prompt.txt)" --permission-mode acceptEdits
```

where the txt file holds the filled-in prompt above. Headless runs need a
persistent browser profile that stays logged in to Facebook; if login state is
flaky, prefer running it inside an interactive session instead.

## Cadence and timing

- **Weekly is a sane Facebook default.** Facebook flags fast automated behavior
  harder than the follow-graph platforms, and once the obvious junk is gone
  each run only nets single digits — so there's little upside to running daily
  and real downside (checkpoints) to running fast.
- Pick a consistent-ish human hour (weekend morning works). Avoid exact
  midnight-style times.
- For a big backlog, prefer one **batch-authorization** sitting (census file +
  one bulk yes — see `SKILL.md`) over cranking the cap up. It clears years of
  cruft in one authorized pass with every safety check still on.
- If two consecutive runs log throttling, checkpoints, or action blocks, drop
  the cap (50 → 25) and/or skip a week before resuming.

## What the scheduled run must NOT do

- Run the setup pass again, re-interview you, or rewrite the keep/cut rules on
  its own (mid-run rulings you give still get captured — that's different).
- Unfriend anyone, or leave any admin/moderated group, without your explicit
  sign-off in the queue file.
- Act on anything in "Needs your sign-off" or "Needs your eyes" that you
  haven't explicitly answered.
- Retry after a throttle, checkpoint, or action block within the same run.
