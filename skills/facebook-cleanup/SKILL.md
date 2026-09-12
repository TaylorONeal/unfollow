---
name: facebook-cleanup
description: Review Facebook Pages, groups and people using private user preferences. Distinguish unfollow, unlike, leave and unfriend; protect contacts and admin roles, with explicit action-specific approval.
---

# Facebook Feed and Membership Review

Read [Security](references/SECURITY.md) and
[Personalization](references/PERSONALIZATION.md) before acting. Missing references
mean read-only. This is a public reusable skill; private preferences and queues
stay outside the checkout and installed skill. Do not import another user's rules.

## Workflow

1. Confirm the active account and reuse its private profile. For first use,
   follow shared setup and make a bounded, read-only census. Discover actual
   tools rather than assuming a browser route or native feature exists.
2. Refresh opted-in protection sources. Screen protected IDs, possible contacts,
   keep lanes, mutuals and uncertain targets before proposing anything.
3. Group candidates by the user's confirmed goals and show exact targets,
   evidence and proposed action. Unknown inactivity/identity remains review.
4. Execute only a valid frozen itemized batch through permitted tools. Recheck
   account, target identity, relationship, protections and grant before every
   action. No broad rule can add new candidates to an approved batch.
5. Journal intent before action, verify target-specific resulting state, then
   record the outcome. Enforce shared caps and a single writer. Stop on any
   restriction or unknown outcome; do not blindly retry.
6. Report verified changes and useful new decisions. Deduplicate and stay quiet
   on unchanged scheduled scans. Record user corrections narrowly and prepare
   recovery; do not silently reconnect or widen rules.

Use [the private queue template](queue-template.md) and
[the scheduling prompt](scheduled-task.md). Existing legacy cut preferences
need migration; they are not execution grants.

## Distinct actions, distinct consent

| Surface | Preferred proposal | Higher-consequence action |
|---|---|---|
| Page | Unfollow to reduce feed content | Unlike is separate; never substitute it |
| Person | Unfollow only when explicitly selected; keep friendship | Unfriend requires interactive itemized approval |
| Group | Adjust feed preference only if requested | Leave requires interactive itemized approval and role check |

No general "clean Facebook" instruction chooses among these actions. Standing
batches cover only exact non-person Page unfollows, never unlike, groups or people.
Protected people are never automatically changed. Do not claim full reversibility:
rejoining/refriending can require approval or fail. Leaving a group may lose access.

Before a group leave verify member/admin/moderator status and whether the user is
last admin. Unknown role is a hold. Last-admin departure needs a separate verified
handover plan; never orphan a group as incidental cleanup. Do not toggle prevent
reinvite, report a group or accept additional actions in a post-leave prompt.

## Census and UI hazards

Work with what loads; no fixed 20-card or eight-row assumptions. Different sorts
may reveal more items, but deduplicate by ID and disclose incomplete coverage.
An alternate sort cannot expand a frozen approved batch. A blank surface is
unknown coverage, not permission to guess or claim throttle. If an account-wide
restriction appears, do not switch surfaces to continue cutting.

Reacquire the current item and action after every grid reflow. Confirm the dialog
names the intended target and action; cancel mismatches before continuing. No
scroll/click while an unresolved modal is open. Verify membership/follow state
on the target rather than inferring success from total count changes.

## Personal context

Do not assume a previous city, employer or school is irrelevant. Relationship,
alumni and community groups may remain valuable. Let the user confirm specific
outdated logistics interests; preserve relationship ties. Fresh explicit
corrections supersede stale rules within their stated scope, without repeated
questions; unresolved ambiguity gets one focused question.
