# Architect Plan

I need your help to write out a comprehensive implementation plan.

## Context

Assume that the engineer has zero context for our codebase & questionable taste. Document everything they need to know:
- Which files to touch for each task
- Code examples and patterns to follow
- Testing strategies and examples
- Documentation they might need to check
- How to test each change

## Requirements

Give them the whole plan as bite-sized tasks following these principles:
- **DRY** (Don't Repeat Yourself)
- **YAGNI** (You Aren't Gonna Need It)
- Frequent commits with clear messages

## Audience

Assume they are:
- ✅ A skilled developer
- ❌ Unfamiliar with our toolset
- ❌ Limited knowledge of our problem domain
- ❌ Inexperienced with good test design

## Output Format

Please write out this plan, in full detail, into:
```
project-root/plans/YYYY-MM-DD/[feature-name].md
```

Make sure to include:
1. **Overview** - High-level description of the feature/change
2. **Prerequisites** - What to review/understand first
3. **Task Breakdown** - Numbered, sequential tasks with:
   - Files to modify/create
   - Specific code changes needed
   - Test cases to write (with examples)
   - How to verify the change works
4. **Commit Strategy** - Suggested commit points
5. **Validation** - Final testing checklist

