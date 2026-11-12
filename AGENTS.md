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

## Secrets

Never write a secret into any file: code, config, docs, notes, prompts, submission notes, logs or commit messages. That covers API keys (RevenueCat and Stripe `sk_` keys included), Supabase service role keys and JWT secrets, access tokens (Codemagic, Vercel, GitHub, Expo), deploy keys, and the contents of `.p8` or `.pem` files. Write where the secret lives instead, for example "RevenueCat dashboard > API keys" or "Supabase secrets", and read it from an environment variable. The secret-scan check (`.github/workflows/secret-scan.yml`, rules in `.gitleaks.toml`) fails any pull request that adds one. If a real secret was ever committed, removing it is not enough: say so, so the key gets rotated.
