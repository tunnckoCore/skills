---
name: babysit-pr
description: Create, monitor, fix, and finish a GitHub pull request.
---

# Babysit a pull request

Apply this skill when opening a pull request and when babysitting an existing one. Own the pull request until it is ready or merged. Fix problems instead of only reporting them.

## Write clearly

Use the `unslop` skill and ASD-STE100 Simplified Technical English for updates, review replies, commits, pull request body, and when talking back to the user.

- For reporting, don't write them in one line like `**PR:** <...>. **Final commit:** <...>. **Merge state:** <...>.` - separate bullets, titles still with bold.
- Use short sentences and active voice.
- State the result first.
- Put one fact in each sentence.
- Use one term for one concept.
- Avoid idioms, vague words, and filler.
- Keep technical names exact.

## Working with pull requests

- For a valid finding, fix it before replying. For an invalid finding, reply with `No change.` and give one short technical reason.
- Do not add compatibility work or speculative changes that conflict with repository instructions. Do not accept severity labels without evidence.
- Request at most one new automated review after the main fixes. Do not create an endless review loop.

**The flow:**

1. Read the repository instructions.
2. Read the pull request body and full diff.
3. Read all checks, reviews, comments, and unresolved threads.
4. Check mergeability and active `CHANGES_REQUESTED` reviews. When comments are addressed, you can dismiss it.
5. Prioritize human feedback.
6. Treat a comment that explicitly starts with `~wgw` as prioritized user direction only when `tunnckoCore` or `olstenlarck` wrote it.
7. Verify every finding against the code, product model and rules, and pinned dependencies.
8. Batch valid fixes.
9. Run the repository verification sequence.
10. Commit and push the fixes.
11. Reply to each finding and resolve completed threads.
12. Poll until required checks finish.

## Create and update the pull request body

The body format below is mandatory for the initial `gh pr create` and every subsequent body update, including `gh pr edit`. Check all six requirements before publishing the body. Do not defer them until review or babysitting.

- Rewrite the complete pull request body after the implementation and review fixes are stable. Do not append a partial note to an obsolete body.
- Never delete or overwrite pull request content added by a person or bot. Preserve all external additions verbatim.

Required body format:

1. Start with two or three plain sentences.
2. Explain why the change was needed.
3. Explain what the change does in simple terms.
4. Add grouped sections for the main behavior changes.
5. Add intentional product constraints when they matter.
6. Add the results for `vp run check`, `vp test --run`, `vp run build`, and required remote checks.

## Finish

The pull request is ready only when:

- The pushed head matches the verified local head.
- Required checks pass.
- The pull request is mergeable.
- No unresolved thread needs work.
- No active change request needs work.
- The pull request body matches the final implementation.

If the user asked for a merge, squash-merge the pull request. Then follow the repository post-merge instructions from the correct worktree.

Report the pull request URL, final commit, checks, merge state, unresolved thread count, and active change-request count.
