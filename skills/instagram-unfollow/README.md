# instagram-unfollow (coming next)

Placeholder for the Instagram version of the following-cleanup skill. Not
implemented yet — do not point an agent at this folder.

It will share the same design as `x-unfollow`:

- Slow capped drip, never a one-shot purge
- A local queue file as the source of truth
- Protected contacts built from DMs, Gmail, and Contacts before anything is cut
- Profile-verified cuts only, uncertain accounts to a review lane

Instagram-specific differences to design for:

- **Much harsher action limits.** Instagram action-blocks aggressively;
  community consensus puts safe unfollow volume around 50-60/day for aged
  accounts and far lower for new ones, with per-hour pacing mattering as much
  as per-day totals. The default cap will be lower than X's and spread within
  the run.
- **Mobile-web vs desktop-web differences** in how the following list renders
  and paginates.
- **"Least interacted with" and "Most shown in feed" native lists** — Instagram
  exposes its own cleanup candidates under Following → Categories, which the
  skill should read first as a high-signal candidate source before touching the
  full list.
- **Close Friends and mutual-DM signals** as native protected-contact sources.
