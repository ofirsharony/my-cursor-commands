# Go to Master and Pull

Switch to the master (or main) branch and pull the latest changes.

## Steps

1. Check for uncommitted changes by running `git status`.
2. If there are uncommitted changes (staged, unstaged, or untracked), first check whether the current branch has already been merged into master/main:
   ```bash
   git branch --merged master
   ```
   (or `main` if master doesn't exist). Then ask the user how to handle the uncommitted changes, offering these options:
   - **Stash** — run `git stash --include-untracked` before switching. After pulling on master/main, inform the user their changes are stashed and can be restored with `git stash pop`.
   - **Discard** — throw away all uncommitted changes with `git checkout -- . && git clean -fd`, then switch.
   - **Ignore** — proceed without doing anything about the changes (they will carry over to the target branch as unstaged changes).
   - **Move to master/main** — carry the changes over intentionally and stage them on master/main after switching. Run `git add -A` on master/main so the user can commit them there.
   - **Commit on current branch first** — commit the changes on the current branch before switching. Use the git-commit-push skill if available. **Only offer this option if the current branch has NOT been merged into master/main.**
3. Switch to master and pull:
   ```bash
   git checkout master && git pull
   ```
4. If `master` doesn't exist, try `main` instead:
   ```bash
   git checkout main && git pull
   ```
5. Report the result including any updated files.
