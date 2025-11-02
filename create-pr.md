# Create PR
## Overview
Create a well-structured pull request with proper description, labels, and reviewers based on current changes.

## Steps
1. **Prepare branch**
   - Ensure all changes are committed
   - Push branch to remote
   - Verify branch is up to date with main

2. **Write PR description**
   - Summarize changes clearly
   - Include context and motivation
   - List any breaking changes
   - Add screenshots if UI changes

3. **Create PR using GitHub CLI**
   - Create a temporary PR description file using `echo -e` with `\n` for newlines
   - Use the **GitHub CLI** with the `--body-file` flag pointing to the temporary file
   - Always use explicit `echo -e "content\nwith\nnewlines"` syntax instead of heredocs or direct newlines
   - Remove the temporary file after PR creation
   - Add appropriate labels

## Technical Implementation
This approach ensures all newlines are properly escaped when creating the PR description file, preventing the "Command contains newline characters" error.

Example:
```bash
# Create temporary PR description file
echo -e "## Summary\nDescription here\n\n## Changes\n- Change 1\n- Change 2" > /tmp/pr_description.txt

# Create PR using GitHub CLI
gh pr create --title "PR Title" --body-file /tmp/pr_description.txt --label "enhancement"

# Clean up
rm /tmp/pr_description.txt
```

## PR Template
- [ ] Feature A
- [ ] Bug fix B
- [ ] Unit tests pass
- [ ] Manual testing completed