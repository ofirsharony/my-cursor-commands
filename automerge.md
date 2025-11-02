# Automerge Command

Enable auto-merge for a GitHub pull request. The PR will automatically merge when all branch protection requirements are met (e.g., required reviews, status checks).

## Usage

Run this command when you want to enable auto-merge for a pull request.

## Steps

1. Get the PR number (either from context or ask the user)
2. Run the GitHub CLI command to enable auto-merge:
   ```bash
   gh pr merge [PR-NUMBER] --merge --auto | cat
   ```
3. Confirm that auto-merge has been enabled

## Notes

- The `--merge` flag specifies to use a merge commit (not squash or rebase)
- The `--auto` flag enables auto-merge rather than merging immediately
- The `| cat` ensures clean output
- The PR will merge automatically once all branch protection requirements are satisfied
