# Public repository boundary

This repository is the open-source social cleanup skill package, separate from
all personal installations and from the Gmail repository.

- Never copy actual profiles, handles, contact lists, schedules, journals,
  screenshots, private paths or preferences into this repository or its PRs.
- Use synthetic fixtures and empty templates. Personal projects require separate
  directories, state and commits. Do not read personal state to populate examples.
- Share generic improvements only after removing personal assumptions. Never
  overwrite a personal installation during a public update.
- Before publication inspect the diff, tracked files and commit metadata; use a
  public noreply identity. .gitignore is not a safeguard for tracked data.
- Bundle only explicitly listed public files. Keep source references synchronized
  with `python3 scripts/check.py --sync` and run checks/tests after changes.
- Update docs/INDEX.md when documentation changes. Live social-account actions
  are not part of repository tests or publishing authorization.
