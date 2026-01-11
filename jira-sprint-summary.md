# Jira Sprint Summary

## Overview
Extract Jira sprint summary with estimations by assignee for a given board and sprint.

## Parameters
- **board** (required): Project/board key (e.g., "DE", "PROJ", "TEAM", "ENG")
- **sprint** (required): Sprint name or number (e.g., "216", "R&D Mon 212")

## Usage
```
jira-sprint-summary board=DE sprint=216
```

## Workflow

### Step 1: Get Atlassian Cloud ID

Use the Atlassian MCP tool to get accessible resources:
```
Tool: getAccessibleAtlassianResources
Arguments: {}
```

Extract the `cloudId` from the response.

### Step 2: Find Sprint ID (if needed)

If the user provided a sprint name/number but you need the sprint ID, search for it:
```
Tool: searchJiraIssuesUsingJql
Arguments: {
  "cloudId": "<cloudId>",
  "jql": "project = <BOARD> AND sprint is not EMPTY",
  "fields": ["customfield_10020"]
}
```

Look in the sprint field (`customfield_10020`) to find the sprint ID matching the sprint parameter.

### Step 3: Query Sprint Issues

Query all issues in the sprint (excluding closed sub-tasks):
```
Tool: searchJiraIssuesUsingJql
Arguments: {
  "cloudId": "<cloudId>",
  "jql": "project = <BOARD> AND sprint = <sprintId> AND status != Closed",
  "fields": ["summary", "timeoriginalestimate", "timetracking", "assignee", "status", "issuetype", "subtasks", "parent"]
}
```

Note: The `parent` field is needed to identify the parent story for sub-tasks.

### Step 4: Process and Filter Data

#### ⚠️ Important: Avoiding Double-Counting

Stories with subtasks should **NOT** be included in the totals because:
- The story's estimation would double-count work already tracked in subtasks
- Only the subtasks represent actual work items to be done

#### Filtering Rules

| Issue Type | Has Subtasks? | Include? |
|------------|---------------|----------|
| Sub-task | N/A | ✅ Yes |
| Story | No (empty array) | ✅ Yes |
| Story | Yes | ❌ No - skip it |
| Task | N/A | ✅ Yes |
| Bug | N/A | ✅ Yes |

#### What to Extract

From the results, extract:
- **Sub-tasks**: All sub-tasks (these represent actual work items)
- **Stories without subtasks**: Stories where `subtasks` array is empty (standalone work items)
- **Tasks/Bugs**: Include regardless of subtasks
- **Exclude**: Stories that have subtasks (to avoid double-counting)

For each issue, extract:
- `key`: Issue key (e.g., "DE-1510")
- `summary`: Issue title
- `issuetype.name`: "Sub-task", "Story", "Task", "Bug"
- `assignee.displayName`: Person assigned
- `timetracking.remainingEstimateSeconds`: Remaining estimation in seconds (divide by 28800 for days)
- `timetracking.remainingEstimate`: Human-readable remaining estimation (e.g., "2d", "0.5d")
- `parent.fields.summary`: Parent story title (for sub-tasks only)
- `parent.key`: Parent story key (for sub-tasks only)

### Step 5: Generate Output

**ALWAYS include all four sections below, even if the user doesn't explicitly ask for them.**

#### Section 1: Per Assignee Tables

For each unique assignee, create a table with a **Story** column showing a 2-4 word summary of the parent story.

**Sorting**: Group sub-tasks by their parent story so related tasks appear together. Stories without subtasks appear at the end.

**Story Column Values**:
- For sub-tasks: A 2-4 word summary of the parent story title (e.g., "Data Analyst Agent", "EMR Upgrade", "Sprint Stabilization")
- For standalone stories/tasks: Use "—" (em dash)

```markdown
## [Assignee Name] (sub-tasks + stories without subtasks, excluding closed)

| Key | Story | Summary | Type | Remaining Estimation | Link |
|-----|-------|---------|------|---------------------|------|
| DE-XXXX | Data Analyst Agent | Task description | Sub-task | Xd | <site-url>/browse/DE-XXXX |
| DE-XXXX | Data Analyst Agent | Another related task | Sub-task | Xd | <site-url>/browse/DE-XXXX |
| DE-XXXX | Sprint Stabilization | Different story task | Sub-task | Xd | <site-url>/browse/DE-XXXX |
| DE-XXXX | — | Standalone story | Story | Xd | <site-url>/browse/DE-XXXX |
...

**📊 Total: X.X days** (N tasks)
```

#### Section 2: Overall Summary Table

```markdown
## 📈 Overall Summary

| Assignee | Tasks | Total Remaining |
|----------|-------|-----------------|
| Name 1 | N | **X days** |
| Name 2 | N | **X days** |
| **Team Total** | **N** | **X days** |
```

#### Section 3: ⚠️ Unestimated Tasks (ALWAYS SHOW)

**Always** include a section listing tasks without remaining estimation, even if the user doesn't ask.

**Important**: Only show items that genuinely need remaining estimation:
- Sub-tasks without remaining estimation → ✅ Show
- Stories **without** subtasks and without remaining estimation → ✅ Show  
- Stories **with** subtasks (regardless of story estimation) → ❌ Don't show (tracked via subtasks)
- Tasks/Bugs without remaining estimation → ✅ Show

```markdown
## ⚠️ Unestimated Tasks

| Key | Summary | Type | Assignee | Link |
|-----|---------|------|----------|------|
| DE-XXXX | Task without estimation | Sub-task | Person Name | <site-url>/browse/DE-XXXX |
...

**Total: N tasks without estimation**
```

If there are no unestimated tasks, just show (without the header):
```markdown
✅ All tasks have estimations!
```

#### Section 4: ⚠️ Unassigned Tasks (ALWAYS SHOW)

**Always** include a section listing tasks without an assignee, even if the user doesn't ask:

```markdown
## ⚠️ Unassigned Tasks

| Key | Summary | Remaining Estimation | Link |
|-----|---------|---------------------|------|
| DE-XXXX | Task without assignee | Xd | <site-url>/browse/DE-XXXX |
...

**Total: N tasks without assignee**
```

If there are no unassigned tasks, just show (without the header):
```markdown
✅ All tasks have assignees!
```

## Estimation Conversion

- `timetracking.remainingEstimateSeconds` is in seconds
- 1 day = 28800 seconds (8 hours)
- 0.5 day = 14400 seconds
- 0.25 day = 7200 seconds

Formula: `days = remainingEstimateSeconds / 28800`

**Note**: Use remaining estimation (not original estimation) to reflect current sprint workload.

## Link Format

Jira links follow this pattern:
```
<site-url>/browse/<ISSUE-KEY>
```

The `<site-url>` is derived from the `url` field in the `getAccessibleAtlassianResources` response (e.g., `https://yourcompany.atlassian.net`).

## Workflow Summary

**Command**: `jira-sprint-summary board=<BOARD> sprint=<SPRINT>`

**What the assistant does**:
1. Gets cloudId using `getAccessibleAtlassianResources`
2. Finds sprint ID by searching for sprints in the project using the board parameter
3. Queries: `project = <BOARD> AND sprint = <sprintId> AND status != Closed`
4. Filters out stories with subtasks (to avoid double-counting)
5. Groups results by assignee
6. **Automatically shows unestimated and unassigned tasks**

The output will follow the format templates defined in Step 5 above, populated with actual data from Jira.
