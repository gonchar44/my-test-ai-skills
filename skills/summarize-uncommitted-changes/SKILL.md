---
name: summarize-uncommitted-changes
description: Summarize what changed in the current Git working tree, including staged, unstaged, and untracked changes. Use for a concise recap of work before committing or handing it off.
---

# Summarize uncommitted changes

Explain what the current changes do in a short, factual summary. Inspect the repository with read-only commands and leave its state intact.

## Inspect the changes

1. Run `git status --short --untracked-files=all` from the repository root. If the working tree is clean, say so and stop. If the current directory is outside a Git repository, report that.
2. Read `git diff --cached` for staged changes and `git diff` for unstaged changes. For large diffs, start with `--stat` for each and inspect the relevant patches by path. If a file appears in both diffs, account for both versions before describing its current behavior.
3. Inspect relevant untracked files because they do not appear in `git diff`. For binary, generated, or unreadable files, report what you can verify from their paths or metadata. Keep secret values out of the summary.

## Write the summary

- Group related edits by outcome. Lead with the most significant change and explain its effect on users, behavior, or maintenance when the evidence supports it.
- Use one bullet per meaningful change, typically 2–5 bullets total. Include key paths only when they help locate the work. Fold related tests, docs, or configuration into the same bullet.
- Mention staged and unstaged differences when they affect what would be included in a commit. Flag unresolved conflicts or material inspection gaps when present.
- Describe what the changes show. Claim a reason, fix, or test result only when supported by the available evidence.
