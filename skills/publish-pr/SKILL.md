---
name: publish-pr
description: Use when the user asks to create or publish a PR from local changes.
---

# Publish PR

## Overview

Use this skill for the full local-to-GitHub PR flow: inspect changes, confirm scope, create or reuse a branch, commit, push, and open a draft pull request. Keep naming human-readable.

## Naming

Use a type prefix consistently on the branch, commit, and PR title.

Common types:

- `feat`: user-facing feature
- `fix`: bug fix
- `test`: test-only change
- `docs`: documentation-only change
- `refactor`: restructuring without behavior change
- `chore`: maintenance, tooling, dependency, or repo hygiene
- `perf`: performance improvement
- `ci`: CI or release automation

Formats:

```text
branch: type/short-kebab-summary
commit: type: imperative summary
PR:     type: reviewer-facing summary
```

Examples:

```text
branch: test/api-error-smoke-contract
commit: test: add API error contract smoke tests
PR:     test: add smoke coverage for API error responses
```

Prefer the narrowest honest type. If the repo has a stricter local convention, follow the repo.

## Workflow

1. Confirm intended scope.
   Run `git status -sb` and inspect the diff before staging. If the worktree contains unrelated changes, ask which files belong in the PR. Never stage unrelated user changes silently.

2. Choose the type and summary.
   Derive a concise type and summary from the change. Ask the user if the type is ambiguous or the repository convention is unclear.

3. Determine branch strategy.
   If on `main`, `master`, or the default branch, create `type/short-kebab-summary`. Otherwise stay on the current branch unless the user asks for a new branch.

4. Stage only intended files.
   Prefer explicit file paths. Use `git add -A` only when the user has confirmed the whole worktree belongs in scope.

5. Commit.
   Use `type: imperative summary`. Keep it terse.

6. Run relevant checks.
   Use the project’s existing scripts. If checks already ran after the final diff, reuse that result. If a check fails because dependencies or tools are missing, install what is needed only when appropriate for the repo and rerun once.

7. Push.
   Resolve the current branch, then push it with tracking: `git push -u origin <branch>`.

8. Open a draft PR by default.
   Prefer the GitHub app or connector for PR creation after pushing. Use `gh pr create` only as fallback. Use a typed title: `type: reviewer-facing summary`. Do not mark ready for review unless the user explicitly asks.

9. Summarize.
   Include branch, commit, PR URL, target branch, checks run, and any caveats.

## PR Body

Use this structure for the PR body:

```markdown
## Summary

What changed, why, and its impact. Include the root cause for fixes.
A small visual when it clarifies the change.

## Evidence

Before/after screenshots, test results, or relevant output.

## Risk and rollback

**Door:** One-way or two-way, with the rollback implications.
**Blast radius:** The users, components, or behavior that could be affected.
```

Skip preambles and keep prose brief and specific to the diff. Use the project's domain language.

End the PR body with a blockquote naming the model(s), reasoning effort, and harness used in the thread: `> By: <model> <effort> on <harness>`, for example `> By: GPT-6.1-Sol High on Codex`. Omit unknown details rather than guessing; omit the attribution if the model is unknown.

### Summary

Explain the change and its motivation. Pick the smallest view that helps a reviewer understand the important relationship:

- Logic or an algorithm: pseudocode.
- Runtime execution: a call tree.
- UI structure: a component tree, including relevant state and ownership boundaries.
- File responsibilities or a broad refactor: a shallow file tree.
- Component interaction or data flow: a Mermaid diagram.
- A change to an existing structure: a diff sketch.

For example, a diff sketch can show a control-flow change:

```diff
 on(save)
-  write content
+  if content is unchanged
+    return cached result
+  write content
+  invalidate cache
```

Place visuals beside the short text they support. Include only the calls, files, states, and boundaries that explain the change. Simple changes can use prose alone; show a whole code block when omitted context would hide ownership or execution order.

### Evidence

Show concrete evidence that the change works. Prefer before/after evidence: screenshots for visual changes, a regression test failing before and passing after for fixes, or relevant execution output. Include the actual checks run and their results; when before evidence is unavailable, report the validation you performed without inventing a comparison.

#### UI Images

For UI changes or additions, include screenshots in the PR description, with before/after images when useful. Use Markdown image syntax with a relative file path (for example, `./screenshot.png`) and pass the same file with `--attach ./screenshot.png` to `gh pr create` or `gh pr edit`. Repeat `--attach` for multiple images; GitHub CLI uploads them and replaces the local paths with hosted URLs.

### Risk and rollback

Describe whether the change is a two-way door (straightforward to reverse) or a one-way door (destructive or hard to reverse). Explain any rollback constraints, such as data changes that reverting the code would not undo.

Describe the blast radius in concrete terms: affected consumers, shared behavior, layout, responsiveness, or data. Keep the assessment proportional to the change rather than reducing risk to a one-word label.

## Safety

- Never rewrite history unless explicitly requested.
- Never force-push unless explicitly requested.
- Never push without confirming scope when the worktree is mixed.
- Stop and explain the blocker if the repository lacks an accessible GitHub remote.
