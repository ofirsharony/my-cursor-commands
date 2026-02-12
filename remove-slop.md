# Remove AI code slop

Check the uncommitted diff (`git diff` and `git diff --cached`) and remove all AI-generated slop from the changed files. Before editing a file, read it in full to understand its existing style.

This includes:
- Extra comments that a human wouldn't add or are inconsistent with the rest of the file
- Extra defensive checks or try/catch blocks that are abnormal for that area of the codebase (especially if called by trusted / validated codepaths)
- Unused or redundant imports
- Debug leftovers you think are not needed anymore (console.log, print statements, etc.) that weren't in the original code
- Premature abstractions — interfaces, enums, wrapper functions, or helper classes that have only one caller and aren't needed yet
- Obvious copy-pasted blocks within the same file — but do not extract shared abstractions or helpers; only remove truly redundant duplicates
- Type workarounds added to bypass type or lint errors (`any` casts, `as unknown as X`, `@ts-ignore`, `@ts-expect-error`, `eslint-disable`, etc.)

Do not change any logic or behavior — only remove cosmetic slop. When in doubt, leave the code as-is.

Report at the end with only a 1-3 sentence summary of what you changed.
