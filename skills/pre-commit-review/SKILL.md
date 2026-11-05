---
name: pre-commit-review
description: Quickly review local uncommitted Git changes and recommend fixes before committing. Use when asked for a pre-commit review, a sanity check of local changes, or a review of what will go into the next commit.
---

# Pre-commit review

Review local Git changes and report concrete fixes the user can make before committing. Keep the review local and read-only. Editing files, staging changes, and creating commits require separate user instructions.

## Identify what to review

- Confirm the intended Git repository and inspect `git status --short --untracked-files=all`. If there are unresolved merge conflicts, report the affected paths and flag them as blockers.
- For a general review of uncommitted changes, inspect `git diff --cached` for staged changes, `git diff` for unstaged changes, and read relevant untracked files directly. If the same file has staged and unstaged edits, distinguish the two versions when reporting findings.
- If the user asks what will go into the next commit, review the staged diff. Mention any unstaged or untracked changes that the commit would omit, especially if the staged code appears to depend on them. Use the staged version when reading surrounding code in a partially staged file. Respect any narrower scope the user specifies.
- If the requested scope is empty, say so. For large diffs, prioritize behavioral changes and disclose which parts you skipped.

## Review the changes

Read enough surrounding code, callers, and tests to verify each suspected issue. Prioritize regressions, security and data risks, broken contracts, and failure paths. Recommend a focused test when it would catch a concrete regression. Skip style preferences and speculative concerns.

Use lightweight local checks when they can substantiate a finding and fit the read-only review. State what the checks covered, especially if they ran against the working tree while the review targeted staged content. If you find a likely secret, report its location and type without reproducing the value.

## Report findings

Lead with actionable findings in severity order. For each finding, give the file and line when available, identify whether the issue is staged, unstaged, or untracked when that matters, explain what breaks and under what conditions, and recommend the smallest practical fix.

If there are no actionable findings, say so within the scope reviewed. Briefly note any material gaps in coverage or verification.
