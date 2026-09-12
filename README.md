# unfollow

Public, portable agent skills for making social feeds more intentional. Review
X/Instagram following lists and Facebook Pages, groups and people against the
installing user's confirmed preferences. Protect relationships, prepare exact
decisions, and execute only explicitly approved batches where permitted tools
support the action. This is an instruction package, not a running automation
service or an enforced security boundary.

| Skill | Scope |
|---|---|
| [x-unfollow](skills/x-unfollow/SKILL.md) | X review/export; manual execution by default |
| [instagram-unfollow](skills/instagram-unfollow/SKILL.md) | Following review with private-account and relationship protection |
| [facebook-cleanup](skills/facebook-cleanup/SKILL.md) | Separate Page unfollow, unlike, people and group decisions |

## Useful personalization

Start with “Review my following list and suggest what I can safely stop seeing.”
The assistant previews a bounded sample, proposes keep lanes, asks for missing
protections and records only the user's confirmed choices. Native rankings and
old visible posts are hints, never proof of disinterest. Private or inaccessible
profiles do not establish inactivity. Past cities or jobs do not erase people.

The default is read-only. One reviewed batch can authorize exact targets and
an exact action with an expiry, so the assistant need not ask before each click.
It cannot add newly discovered accounts to that batch. Fresh identity,
relationship and protection checks still apply. Unknown evidence means hold.

Unattended work focuses on discovery, deduplicated reviews and eligible approved
non-person unfollows through permitted integrations. No unattended groups,
private accounts, personal contacts, mutuals, unlike, unfriend, reporting or
blocking. X's rules restrict website scripting; the X skill defaults to a useful
manual queue unless a permitted official integration is verified. See
[Security](SECURITY.md) for platform sources and execution conditions.

Internal caps (10 attempted actions/run, 20/account/day) limit potential loss;
they are not platform-approved rates or guarantees against account restrictions.
No warm-up ladder, anti-detection behavior or guaranteed completion times.

## Public and personal stay separate

This repository contains reusable instructions and empty templates only. Personal
versions live in separate private projects with their own state, schedules and
commits. Never bake actual contacts, handles, interests or private paths into the
public skills. [AGENTS.md](AGENTS.md) records that boundary for future changes.

Profiles, queues and journals belong outside both this checkout and installed
skill folders. Each platform/account has separate state; Facebook surfaces share
one account cap and writer lock. Public updates never overwrite personal state
or migrate personal schedules. Installation does not grant account access.

## Install

Python 3.10+ is recommended. Installer and tests use only the standard library.
Install to the directory your agent uses, for example:

```sh
python3 scripts/install.py --dest "$HOME/.codex/skills"
```

Use `--skill x-unfollow` to install just one. The installer bundles only explicitly
listed files plus shared references, excluding extra local state and rejecting
linked source files. Existing targets are never overwritten. Install updates to
a staging directory first and review them before replacing an existing package.
Interrupted installs may leave partial new folders; errors identify that risk.

Each source skill also includes synchronized references for standalone folder
installation. Prefer the manifest installer if your source folder could contain
extra local files. Copying a SKILL.md alone is insufficient.

## Recovery and upgrades

Each attempted action needs a durable pending journal and target-specific
read-back. Unknown outcomes stop writes; retrying a toggle can reverse an action.
Refollowing or rejoining may require approval and cannot be guaranteed. Prepare
recovery for exact logged targets; never reconnect silently.

Preserve old personal queues and review their preferences before migration.
Old `cut` markers, categories and daily caps do not become version 2 action
grants. Existing live schedules retain their behavior until explicitly updated
through their scheduler. A repository merge does not update a personal job.

## Development and verification

Canonical shared instructions are [Security](SECURITY.md) and
[Personalization](PERSONALIZATION.md). After editing:

```sh
python3 scripts/check.py --sync
python3 scripts/check.py
python3 -m unittest discover -s tests -v
```

Checks cover standalone references, drift, missing collections, private-file
exclusion, symlink rejection and preservation of existing installs. They do not
prove agent classification, platform compliance or live account behavior.
See [review and scenarios](docs/REVIEW.md) and [documentation index](docs/INDEX.md).

MIT — see [LICENSE](LICENSE).
