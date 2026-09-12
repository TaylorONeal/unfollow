# Personalization and bounded execution

Optimize for an intentional feed and fewer repeated decisions, not maximum cuts.
Start with a useful preview. These settings belong to the installing user,
never the maintainer or another account.

## Setup

Confirm platform/account, timezone and the user's current goal. Reuse existing
answers. Sample up to 100 followed targets and show coverage, then propose a few
keep lanes using observed interests and the user's stated preferences. Keep
suggestions separate from confirmed rules; no sensitive trait inference.
Ask what would be costly to lose and whether the user prefers mute/unfollow
proposals or relationship removal. Explain the specific action's consequences.

Offer optional relationship metadata sources only if available: native DMs,
Close Friends, contacts or sent-mail correspondents. Each source needs opt-in;
read names/IDs only, not message bodies. Do not auto-open conversation bodies
when a metadata-only interface is unavailable. Explicitly declined sources are
valid; missing/failed opted-in sources must be reported, not treated as empty.
Retain confirmed protections; a source disappearing never deletes protection.

Cross-platform name matches are possible-contact holds until the user confirms
the actual account. In-platform relationships and manual IDs provide stronger
protection. Never cut a stranger simply because no contact match was found.

Save the private profile and queue after confirmation. First run makes no social
actions. Installing, scheduling or approving broad interests grants no cuts.

## Private profile fields (version 2)

- `version: 2`, platform, verified account ID, timezone and setup confirmation.
- Approved sources with explicit opt-in reference, coverage and refresh time.
- Protected target IDs and confirmed keep lanes with source/provenance.
- Goals and review dates; proposed cut categories (proposals only).
- `mode: preview` by default; optional `approved_batch` only with valid grants.
- Limits: scan 100, actions/run 10, actions/day 20; user may lower them.
- Digest preference, last successfully scanned cursor, pending review metadata.
- Grants empty by default. Unrecognized schema, malformed state, missing account
  or unknown daily usage means read-only. Templates must contain no actual users.

Each execution grant records `id`, `account_id`, `platform`, `version`, exact
`target_ids`, exact `action`, `approved_at`, `expires_at`, `revoked_at` and the
user's `confirmation_reference`. Offer a seven-day expiry for a reviewed batch;
never silently renew. Every target is consumed once, including failed/unknown
attempts until reconciled. A changed target, action, account or batch version
requires a new grant. Batch approval is for the displayed frozen census only;
"everything except these" never includes later discoveries.

Unattended execution is available only for individually approved, immutable-ID
public non-person account unfollows (X/Instagram) or non-person Page unfollows
(Facebook), where current official tooling/policy permits it. No unattended
private-account cuts, mutuals, personal contacts, groups, unfriend, unlike,
blocking or reporting. If eligibility cannot be established, prepare a manual
queue. The shared Security contract always applies, including fresh protections.

## Each run

1. Load profile, queue, grants and journal. Confirm signed-in account, current
   capabilities and policy evidence. Missing dependencies mean a preview.
2. Review unresolved outcomes before new writes. Acquire the writer lock; count
   attempted actions across all runs/surfaces in the profile timezone. Never
   reset usage on retries or invoke parallel writers.
3. Refresh protection sources and existing review evidence. Discover candidates
   within the read cap. Preserve cursor and deduplicate by platform/account/target
   ID. Record partial pages as partial, never claim a complete census from counts.
4. Check approved targets before each write. Revalidate identity, grants,
   protection, target type and current relationship. Changed circumstances stop
   that action. Journal pending intent, perform only the exact allowed action,
   verify and record. On a warning/unknown outcome stop writes, not just the item.
5. Prepare new proposals separately from executable grants. Rank by the user's
   explicit unwanted-feed goal, strong evidence and low relationship cost.
   Old or seldom-seen content alone is insufficient. A dense keep set is success.

## Digest and learning

Report verified outcomes, meaningful coverage failures and at most three ordinary
choices. Include target reference, evidence, exact action and consequence. Group
similar proposals for one decision while preserving exact IDs. Never manufacture
urgency or metrics such as time saved. An interactive request gets completion;
a scheduled unchanged scan stays quiet.

Deduplicate pending decisions by account/target/action; record last surfaced,
evidence time, status and next review. Park ordinary unanswered items after three
presentations until a user-chosen review date or meaningful new evidence. No
repeated "still waiting" notifications or silence-as-consent.

A user correction immediately holds the affected batch, records the exact keep
preference with provenance and prepares recovery. Do not generalize to everyone
with similar names, professions or locations. A past city/job never implies
that relationships or alumni groups no longer matter. Distinguish an explicit
change of preference from stale rules; ask only where scope remains unresolved.

## Migration

Preserve old personal queues in their private project. Import confirmed keep
preferences for review, retaining provenance. Old `cut`, daily caps or broad
categories are not v2 action grants. Never rewrite installed personal skills,
create schedules or copy private state into this public repository. Separate
code upgrades from the user's approval of live actions.
