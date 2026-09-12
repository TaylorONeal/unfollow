# Documentation index

- [Overview and installation](../README.md)
- [Public/private repository boundary](../AGENTS.md)
- [Shared security contract](../SECURITY.md)
- [Personalization, grants and private state](../PERSONALIZATION.md)
- [Review and behavioral cases](REVIEW.md)
- [X skill](../skills/x-unfollow/SKILL.md)
- [Instagram skill](../skills/instagram-unfollow/SKILL.md)
- [Facebook skill](../skills/facebook-cleanup/SKILL.md)
- [Manifest installer](../scripts/install.py)
- [Reference checker](../scripts/check.py)

Edit shared root sources, then use `python3 scripts/check.py --sync` to update
each skill's standalone references. Never edit distributed copies independently.
