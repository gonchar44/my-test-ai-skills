---
name: create-new-pr
description: Create a GitHub pull request from the current branch into its immediate parent. Use when asked to open a PR from a branch based on dev or from a child branch in a stack of PRs.
---

# Create a stacked pull request

Open a PR from the current branch into its immediate parent. Apply the same rule at every level of the stack.

| Branch ancestry | PR head | PR base |
| --- | --- | --- |
| `dev → feature/a` | `feature/a` | `dev` |
| `dev → feature/a → feature/b` | `feature/b` | `feature/a` |
| `dev → feature/a → feature/b → feature/c` | `feature/c` | `feature/b` |

## Identify the head and base

1. Identify the current branch with `git branch --show-current`. If HEAD is detached, ask which branch to use. Inspect the configured remotes and upstream to identify the destination GitHub repository and the repository that should host the head. Refresh the relevant remote refs before comparing branches.
2. Look for an open PR from the same head repository and branch. If it already targets the intended base, return its URL. If it targets a different base, report the discrepancy and ask before changing it or opening a second PR.
3. Resolve the immediate parent using the following evidence:
    - Use a base explicitly supplied by the user. Otherwise, check repository-maintained stack metadata and the branch creation entry in `git reflog show <head>`. A creation entry naming a branch is stronger evidence than one naming only `HEAD` or a commit SHA. If it names only `HEAD`, inspect the corresponding checkout entries in the local HEAD reflog.
    - Check existing PRs for relevant branches and compare their tips with the current head. Use `git merge-base` and the commit graph to corroborate the likely parent. If `dev` and a feature branch are both plausible, look for inherited commits that are specific to the feature branch. A candidate's PR into `dev` establishes its own base and serves only as supporting evidence for the current branch.
    - Treat branch names, the head's upstream, and a shared merge base as clues. A rebase, squash merge, deleted branch, or expired reflog can make the original parent ambiguous. If credible evidence conflicts, several candidates remain plausible, or the identified parent no longer exists in the destination repository, summarize what you found and ask for the base. Use `dev` when the evidence identifies it or the user selects it.

## Open the PR

1. Confirm that the selected base exists in the destination repository and differs from the head. Review `git log <base-ref>..<head>` and `git diff <base-ref>...<head>`. If the comparison is empty or includes inherited changes that appear outside the intended PR scope, stop and explain the mismatch.
2. Use the committed changes to write the title and body. Follow the repository's PR template when present. Mention the parent PR when useful and report test results only when known. Leave uncommitted changes untouched.
3. If the user has asked to create the PR, push the current branch to its intended head remote if needed. Push only that branch. If a normal push is rejected, report the failure and preserve the remote history.
4. Create the PR using the GitHub connector or `gh pr create` with the destination repository, head, and base set explicitly. For a fork, identify the head repository as well as the branch. If creation returns an uncertain result, look up the exact head and base before retrying.
5. Verify the resulting PR's head and base on GitHub. Return its URL and the `head → base` mapping. Include a short note on the evidence used to select the base when it was inferred.
