# Transfer Context

Prepare context for a new chat session when this one is degraded or hitting limits.

## Output Format

```
## Context Transfer

### Previous Session
[Chat ID/reference if available]

### Summary
[What was accomplished in this session — outcomes, not attempts]

### Key Decisions
- [Decision 1 and why]
- [Decision 2 and why]

### Important Context
- [Gotchas discovered]
- [Patterns to follow]
- [Things that didn't work and why]

### Relevant Files
Created:
- path/to/new-file.ts - [purpose]

Modified:
- path/to/changed-file.ts - [what changed and why]

Reference (read-only):
- path/to/existing-file.ts - [why it matters]

### Git State
- Branch: [current branch]
- Uncommitted changes: [yes/no, summary]
- Stashed work: [if any]

### Current State
[What's working, what's broken, what's next — in priority order]

### Prompt for New Chat
[An actionable directive for the new session. Not a summary — a concrete instruction of what to do next, with all necessary context inlined. The new session should be able to start working immediately from this prompt alone.]
```

## Instructions

1. Summarize what was accomplished — outcomes, not just attempts
2. List decisions made and their reasoning
3. Note gotchas, failed approaches, and important discoveries
4. Categorize files by created/modified/reference — don't just list them flat
5. Include git state (branch, uncommitted changes, stashes)
6. Describe current state with clear priority ordering
7. The "Prompt for New Chat" must be a self-contained, actionable directive — not a recap. It should tell the new session exactly what to do next, include file paths, and reference any constraints or decisions already made. The new session should never need to ask "what should I do?"
