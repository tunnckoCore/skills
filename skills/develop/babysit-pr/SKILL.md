---
name: babysit-pr
description: Monitor, fix, and finish an open GitHub pull request. Use when the user asks to monitor, watch, or babysit a PR.
---

# Babysit PR

Own the PR until it merges or is ready. Fix problems instead of only reporting them. Use the `prs-and-comments` skill for every comment, reply, body update, and report.

## Start

When the user says "babysit" and T3 Code is available, launch a new T3 Code thread on the PR's worktree with Claude Opus 5.5, medium effort, fast mode. That thread babysits only this PR, never follow-up ones, and sends its final report to the thread that called it.

When T3 Code is unavailable, run this workflow in the current environment on the PR's worktree.

1. Read the repository instructions.
2. Read the PR body and the full diff.
3. Read all checks, reviews, comments, and unresolved threads.
4. Check mergeability and labels. A `do-not-merge` label holds the merge for the owner. Never remove it.
5. Handle what already exists (see "Reviews"), then start the watcher (see "Waiting").

## Waiting

Never write your own watcher, polling loop, `sleep` loop, or `--watch` command. Use one watcher per PR: `watch_pull_request` in T3 Code, `pr-wait` everywhere else. Never run both on the same PR. All watchers on one GitHub account share one rate limit.

### In T3 Code: `watch_pull_request`

Call it and end your turn. T3 Code wakes you when a check fails, the required checks pass, someone comments or reviews, or the branch conflicts.

- **Only the thread that owns the PR watches it.** A delegated child task (a reviewer, a full-review round, any `delegate_task` child) never calls `watch_pull_request`.
- **A wake is news, not a merge decision.** Check the finish gate yourself.
- **Unwatch when you hand off.** When you stop, or ask the user a question, call `unwatch_pull_request` first. A merge or close ends the watch on its own.
- **"Stopped watching, could not read it"** usually means the GitHub rate limit is used up. Run `gh api rate_limit --jq .resources.graphql`. If `remaining` is near 0, wait for `reset` with one background command, then watch again. Never watch again in a tight loop.

### Everywhere else: `pr-wait`

Run `scripts/pr-wait` from this skill's directory, with its absolute path.

```bash
pr-wait <pr> [--repo owner/name] [--until ready|green|any-change]
```

It prints one line per real event. It exits 0 when the `--until` condition is met or the PR merges, 1 when it needs you (a required check failed, conflicts, unresolved threads, PR closed), and 2 on bad input.

- `ready` (default): required checks green, a review bot reviewed the head, and no unresolved threads.
- `green`: required checks only. Use it when the repo has no review bot that `pr-wait` knows.
- `any-change`: exit on the first event.

In Claude Code, run it with the Monitor tool (`timeout_ms: 3600000`), so each line wakes you. This is the only allowed use of Monitor. Start it again after you push, after you handle an exit 1, and after a resume. It remembers what it already reported. If it says another pid already watches the PR, do not start a second one.

With either watcher, when an event needs no action, end your turn without a message. Never post or say "still waiting".

## GitHub rate limits

`gh pr create`, `gh pr view`, and `gh pr checks` use GraphQL. The REST limit is separate and usually still has room when GraphQL is used up. Read the PR with `gh api repos/<owner>/<repo>/pulls/<n>`, and its checks with `gh api repos/<owner>/<repo>/commits/<sha>/check-runs`.

## Reviews

- Act only on checks and comments newer than the latest push.
- Handle human feedback before bot feedback.
- A comment that starts with `~wgw` from `tunnckoCore` or `olstenlarck` is a direct order from the user.
- Verify every finding against the code, the product model and rules, and the pinned dependencies. Do not accept severity labels without evidence.
- Tell repository failures apart from infrastructure flakes.
- Fix a valid finding before you reply. Batch the valid fixes, run the repository verification sequence, then commit and push.
- For an invalid finding, reply `No change.` with one short technical reason, then resolve the thread.
- Keep the PR at the size the goal needs. Decline unneeded tests, guards for edge cases that do not matter, extra compatibility, and small performance costs, and list them as follow-ups. Prefer the fix that deletes or narrows code. Do not add speculative changes that conflict with repository instructions.
- Request at most one new automated review after the main fixes. Do not create an endless review loop.
- Add screenshots or videos when they help and you have a way to upload them.

## Base branch

Rebase when the base branch moves. `pr-wait` reports when new base commits touch this PR's files, and when the PR becomes BEHIND or DIRTY. `watch_pull_request` reports conflicts only, so check for BEHIND yourself before you call the PR ready.

If another PR makes this one obsolete, stop the watcher, report it to the user, and ask before you close this PR.

## Merging

Auto-merge is on for the PR and in the repo settings. GitHub squash-merges the PR when the ruleset is satisfied. Never merge by hand, unless the `full-send` skill runs. The `full-stop` skill turns off auto-merge for that PR.

Before a `full-send` merge, run `git fetch` and check the PR again. Never merge when it is BEHIND or DIRTY, or when base commits that landed after the last green run touch this PR's files. Rebase, push, and wait for green again.

## Finish

The PR is ready only when:

- The pushed head matches the verified local head.
- Every check except `do-not-merge` is green.
- The PR is mergeable, or only the `do-not-merge` label blocks it.
- The branch is not BEHIND.
- No unresolved thread needs work.
- The PR body matches the final implementation.

Stop and report once:

- **Ready, held by `do-not-merge`:** in T3 Code, call `unwatch_pull_request`. Elsewhere, stop the `pr-wait` process if it is still running and ensure it has exited. Report that the label is the only blocker, and end the turn.
- **Merged:** the watch ends on its own. Report the merge commit.
- **Idle:** after one hour without a new event, call `unwatch_pull_request`, report what is still open, and end the turn.

Report:

- **PR:** the URL.
- **Final commit:** the head or merge commit.
- **Checks:** their state.
- **Merge state:** merged, held, or blocked, and why.
- **Unresolved threads:** the count.
