# unfollow

Agent skills for cleaning up social-media accounts safely — following lists on
the follow-graph platforms, and groups/Pages/people on Facebook. Built for
Claude (Cowork / Claude Code with browser automation), portable to any agent
that can drive a browser and edit local files.

Bulk unfollowing is easy to do badly: platforms rate-limit hard, unfollows are
effectively irreversible at scale, and the accounts you most regret cutting are
real people you know. These skills are built around three ideas:

1. **Drip, don't purge.** A small capped batch per day survives rate limits;
   a one-shot purge gets throttled or action-blocked and can get flagged.
2. **A queue file you own is the source of truth.** Every rule, protected
   contact, pending decision, and past action lives in a local markdown file
   you can open, edit, and audit — not in the agent's memory.
3. **Key people are structurally protected.** Before anything is cut, the skill
   builds a protected-contacts list from your DMs, Gmail, and Contacts. A
   protected account is never cut and never even proposed.

## Skills

| Skill | Platform | Status |
|-------|----------|--------|
| [`skills/x-unfollow`](skills/x-unfollow/) | X (Twitter) | Ready |
| [`skills/facebook-cleanup`](skills/facebook-cleanup/) | Facebook | Ready |
| [`skills/instagram-unfollow`](skills/instagram-unfollow/) | Instagram | Ready |

Each skill folder contains:

- `SKILL.md` — the skill itself: hard rules, the setup pass, the daily run
  procedure, and the learning loop
- `queue-template.md` — the template your personal queue/log file is created
  from
- `scheduled-task.md` — the exact prompt and instructions for the daily
  scheduled run

## What it does (X skill)

- Runs as a slow daily drip instead of a one-shot purge, because X throttles
  the `/following` endpoint fast
- Interviews you at setup: scans a sample of your following list, **suggests
  the interest categories it sees**, and lets you confirm/edit them as keep
  lanes instead of asking you to invent them cold
- Builds a **protected-contacts list** from your X DMs, and (if you connect
  them) Gmail sent-mail and Google Contacts, plus anything you add manually —
  these accounts can never be cut
- Buckets every loaded account against your keep and cut rules
- Opens each cut candidate's profile to verify before unfollowing
- Caps cuts per run (default 50) and stops on any rate-limit or action block
- Sends anything borderline to a "Needs your eyes" list instead of cutting
- Logs every decision to a local markdown queue file you own

## What it never does

- Unfollow anyone on the protected-contacts list — not even propose it
- Unfollow accounts in your keep categories, or mutuals, automatically
- Cut accounts it is uncertain about
- Exceed the daily cap, or keep pushing after a throttle or action block
- Unfollow anything during setup
- Read message *content* from DMs/Gmail, or write any of it into the queue
  file — protected-contact building only needs names and handles, and only
  runs for sources you explicitly opt in to

## Setup walkthrough (X)

1. **Install the skill.** Drop `skills/x-unfollow/` into your agent's skills
   directory (for Claude Code: `.claude/skills/x-unfollow/` in a project, or
   `~/.claude/skills/x-unfollow/` globally). Make sure the agent has browser
   automation available and the browser is logged in to your X account.
2. **Say "set up x unfollow".** The setup pass, in order:
   - confirms your **handle** against the live profile and records your
     starting following count;
   - loads a sample of your following list and proposes **keep categories**
     from what it actually sees — you confirm, edit, and add lanes it missed;
   - builds the **protected contacts** list: always offers X DMs (anyone
     you've ever DM'd is protected), and offers Gmail / Google Contacts if
     those connections exist — names from email become a match-list, exact
     matches protect silently, fuzzy matches get flagged for you instead of
     guessed at; you can paste manual handles too;
   - confirms which **cut rules** apply and your **daily cap** (default 50);
   - creates your **queue file** from the template.
3. **Review the dry run.** The first batch is propose-only: you see every
   would-be cut with a one-line reason and veto anything wrong before a single
   live unfollow happens. Vetoes become keep-rule notes.
4. **Schedule the daily run.** Follow
   [`skills/x-unfollow/scheduled-task.md`](skills/x-unfollow/scheduled-task.md)
   — it has the exact prompt to schedule and per-surface instructions (Claude
   scheduled tasks, Claude Code cron, plain OS cron).

## The daily run, day to day

Each scheduled run: reads your queue file → actions any review answers you
left → gently loads a batch of the following list → screens it against
protected contacts → buckets keep / cut / uncertain → opens every cut
candidate's profile to verify → unfollows up to the cap at a human pace →
logs everything → reports in 2-3 sentences.

Your only job between runs is optional: open the queue file, look at "Needs
your eyes", and reply with any handles you're fine cutting. Anything you don't
answer just sits there — silence never turns into a cut.

Expected pace: with the default cap of 50, a 2,000-account list that's ~40%
cuttable takes roughly 3-4 weeks. Low cut counts on some days are normal —
either X throttled the list or that stretch of follows was intentional.

## Facebook cleanup

[`skills/facebook-cleanup`](skills/facebook-cleanup/) applies the same
drip-and-queue design to Facebook, which is not one list but three surfaces
with very different reversibility:

- **Groups → Leave** (reversible; private groups may need re-approval)
- **Pages → Unlike / Unfollow** (fully reversible — the safest surface)
- **People → Unfollow** (stay friends, hide feed — reversible) or **Unfriend**
  (sever the relationship — effectively irreversible at scale)

The core safety idea is that Unfollow and Unfriend are not the same action.
Unfollow can be allowed freely if you opt in; **unfriend is always gated behind
an explicit yes** and never happens automatically. Two more Facebook-specific
guards on top of the shared model:

- **Admin guard.** Groups you admin or moderate are never auto-left (leaving as
  the last admin can orphan a group) — they go to a sign-off lane.
- **Dialog verification.** Facebook's grid drifts and reflows after every
  action, so every Leave/Unlike confirm dialog is screenshot-checked to confirm
  it names the *right* item before the click. This catches wrong-row misfires
  the grid produces in practice.

It runs **weekly** rather than daily (Facebook flags fast automation harder),
and adds a **batch-authorization mode**: because Facebook's list views hard-cap
what they render, the skill cycles all five sort orders to build a fuller
census, writes it to a to-do file bucketed keep/cut/review, and you reply once
("remove everything except X, Y, Z") to authorize a big pass — with every
safety check still on. Setup and the scheduled run work exactly like the X
skill; see [`skills/facebook-cleanup/SKILL.md`](skills/facebook-cleanup/SKILL.md)
and [`scheduled-task.md`](skills/facebook-cleanup/scheduled-task.md).

## Instagram cleanup

[`skills/instagram-unfollow`](skills/instagram-unfollow/) applies the same
drip-and-queue design to Instagram, the strictest platform here. Instagram
action-blocks aggressively and weighs **per-hour** volume as much as the daily
total, so this skill runs the most conservative version of the family:

- **Lower cap, paced within the run.** Default 30/day (vs X's 50), capped at
  ~10 unfollows per rolling hour and spaced ~30-60s apart. New, reactivated, or
  previously-blocked accounts start at a **10/day warm-up tier** and step up only
  if no restriction appears.
- **Native "Least interacted with" as the first candidate source.** Instagram
  exposes its own cleanup lists under Following → Categories; the skill reads
  "Least interacted with" as a high-signal pool before touching the full list.
- **Close Friends is a native protected source**, alongside DMs, Gmail, and
  Contacts — anyone on your green-star list is never cut.
- **Private accounts are never auto-cut on inactivity** — you can't see their
  posts to verify, so they route to review at most.
- **Stops on any restriction** — "Try Again Later", "We restrict certain
  activity", or a challenge — and never attempts to solve a challenge or captcha.

Setup and the scheduled run work exactly like the X skill; see
[`skills/instagram-unfollow/SKILL.md`](skills/instagram-unfollow/SKILL.md), its
[`README.md`](skills/instagram-unfollow/README.md) (with a run-flow diagram), and
[`scheduled-task.md`](skills/instagram-unfollow/scheduled-task.md).

## Safety model

| Risk | Mitigation |
|------|-----------|
| Cutting someone you know | Protected contacts from DMs/Gmail/Contacts, checked before any bucketing; mutuals never auto-cut; real people default to review |
| Wrong-category cut | Profile verified before every unfollow; uncertain → review lane; bias-to-keep is a hard rule |
| Platform action block | Hard cap per run, human pacing, immediate stop on throttle/block/empty list, no same-day retry |
| Runaway automation | Cap is non-configurable upward at runtime; setup never unfollows; scheduled run may not edit its own rules |
| Losing track of what happened | Every load/cut/keep/review decision appended to the queue file with dates and reasons |
| Repeating a mistake | Wrong cuts logged under "Re-follow / mistakes" and converted into protective keep-rule notes |

## Design notes

The queue file is the source of truth, not the agent's memory. Every run reads
it, actions any review decisions you made, appends its results, and leaves the
file tidy for the next run. That makes runs resumable, auditable, and
model-agnostic — you can switch agents mid-cleanup and nothing is lost.

The protected-contacts list is deliberately built from *relationship signals*
(you DM'd them, you emailed them, they're in your contacts) rather than asking
you to remember everyone who matters. Name-based matches from email are fuzzy
by nature, so the skill only auto-protects exact matches; near-matches get
flagged for you rather than silently protected (which would hide cut
candidates) or silently ignored (which would risk a friend).

Wrong cuts get recorded so the same type is protected in future runs — the
system is designed to get more conservative over time, not less.

## Troubleshooting

- **"List renders empty" every run** — X has throttled the endpoint for your
  session. Skip a day, lower the cap, and make sure nothing else is scraping
  the same account.
- **Run says "not logged in"** — the automated browser profile lost its X
  session. Log in once manually in that profile and re-run.
- **It cut something it shouldn't have** — say so. It gets logged under
  "Re-follow / mistakes", re-followed if you want, and a protective rule is
  added. If it was a real person, connect Gmail/Contacts if you haven't.
- **Too slow** — resist raising the cap past 50; that's the reliable ceiling
  before X starts action-blocking. Answer the review lane instead; those cuts
  are the cheap ones.

## Roadmap

- Candidates next: LinkedIn, TikTok, YouTube subscriptions — same drip-and-queue
  design, per-platform caps and protected-contact sources.

## License

MIT — see [LICENSE](LICENSE). No warranty. You are responsible for what your
agent unfollows.
