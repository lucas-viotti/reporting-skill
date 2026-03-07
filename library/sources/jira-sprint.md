# Source: Jira Sprint

Fetches epic and issue status from Jira using JQL queries defined per milestone.

---

## MCP Tool

`mcp__atlassian__searchJiraIssuesUsingJql`

---

## Parameters

- `jql`: JQL query string (from the `milestones[].jql` field in config)
- `fields`: `summary,status,assignee,updated,description`
- `maxResults`: 50

---

## Config Fields

Milestones can be defined inline in `report-config.md`:

```yaml
- type: jira-sprint
  milestones:
    - name: Phase 1
      jql: "key in (PROJ-1, PROJ-2, PROJ-3) ORDER BY status, updated DESC"
      flows:
        PROJ-1: Flow name one
        PROJ-2: Flow name two
        PROJ-3: Flow name three
    - name: Phase 2
      jql: "key in (PROJ-4, PROJ-5) ORDER BY status, updated DESC"
      flows:
        PROJ-4: Flow name four
        PROJ-5: Flow name five
```

Alternatively, milestones can be defined in an external file referenced by `mapping`:

```yaml
- type: jira-sprint
  mapping: milestone_mapping.md   # relative to workspace root
```

When using an external `mapping` file, each section has a `**JQL Query:**` code block —
extract and run each query. Use the issue keys in each section to map to business flow names.

---

## Extraction Instructions

For each milestone, run its JQL query. For each issue returned:

**Extract:**
- `key`: issue key (e.g., PROJ-131)
- `summary`: issue title / flow name
- `status.name`: current status (e.g., "In Progress", "Done", "To Do")
- `assignee.displayName`: owner if present
- `updated`: last update date

Use the `flows` map (inline config) or the mapping file to resolve the business name for each issue key.

**Ignore:**
- Sub-tasks
- Closed/Done issues with no activity in the last 30 days
- Issues in "Won't Do" or "Duplicate" resolution

**Output shape:**
```
{ topic: "<flow name>", status: "<Jira status>", owner: "<assignee or null>", date: "<updated date>", key: "<issue key>" }
```

---

## Notes

- Run one JQL query per milestone as defined in config
- Use `flows` map to resolve business names from issue keys (not raw Jira summaries)
- If an epic listed in `Related Epics` is also a tracked issue, fetch it separately for sub-status
