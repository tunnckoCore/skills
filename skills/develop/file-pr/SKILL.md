---
name: file-pr
description: File a pull request. Use when the user asks to file, open, or create a PR.
---

# File PR

Use the `prs-and-comments` skill for the title, the body, and the disclosure.

1. Check whether a PR for this branch already exists. If one does, update it. Do not open a second PR.
2. Rebase onto the latest default branch.
3. Review the diff against the base branch, usually `origin/master`, and confirm that it matches the goal. One PR holds one concern.
4. Write the title and body with `prs-and-comments`.
5. Open a real PR, not a draft, so review bots run.
6. Turn on squash auto-merge when you create the PR, if the repo allows it. GitHub merges it when the ruleset is satisfied. Never merge by hand.

If `gh pr create` fails because the GraphQL rate limit is used up, open the PR through REST:

```bash
gh api repos/<owner>/<repo>/pulls -f title=... -f head=... -f base=... -F body=@<file>
```

If the user also asked you to babysit the PR, continue with the `babysit-pr` skill.

Post a follow-up comment only when the user's request needs it.
