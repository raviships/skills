---
name: babysit-pr
description: Use when the user asks to babysit a PR.
---

# Babysit PR

Mark the PR ready for review and babysit it until the latest changes have green required checks, completed reviews, and are ready to merge. Repeat review-and-fix cycles as needed.

Evaluate review findings against the code, likely impact, and implementation cost. Fix the ones you judge worth addressing; briefly explain why you decline others.

End PR comments and review replies with a blockquote naming the model, reasoning effort, and harness used for that work: `> By: <model> <effort> on <harness>`, for example `> By: GPT-6.1-Sol High on Codex`. Omit unknown details rather than guessing; omit the attribution if the model is unknown.

After pushing fixes, wait for checks and a fresh review of the latest changes. Some automated reviewers signal no issues with a thumbs-up on the PR description rather than a review comment; check that it applies to the current review cycle.

Do not merge the PR unless the user explicitly asks.
