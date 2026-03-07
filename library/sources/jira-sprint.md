# Source: Jira Sprint

Fetches epic and issue status from Jira using JQL queries defined in a milestone mapping file.

---

## MCP Tool

`mcp__atlassian__searchJiraIssuesUsingJql`

---

## Parameters

- `jql`: JQL query string (read from the mapping file — each milestone/phase section has its own query)
- `fields`: `summary,status,assignee,updated,description`
- `maxResults`: 50

---

## Config Fields

```yaml
- type: jira-sprint
  mapping: milestone_epic_mapping_v2.md   # relative to workspace root
```

The `mapping` file contains JQL queries organized by milestone/phase. Each section has a
`**JQL Query:**` code block — extract and run each query.

---

## Extraction Instructions

For each issue returned:

**Extract:**
- `key`: issue key (e.g., CTRYN-131)
- `summary`: issue title / flow name
- `status.name`: current status (e.g., "In Progress", "Done", "To Do")
- `assignee.displayName`: owner if present
- `updated`: last update date

**Ignore:**
- Sub-tasks
- Closed/Done issues with no activity in the last 30 days
- Issues in "Won't Do" or "Duplicate" resolution

**Output shape:**
```
{ topic: "<flow name from mapping>", status: "<Jira status>", owner: "<assignee or null>", date: "<updated date>", key: "<issue key>" }
```

---

## Notes

- Run one JQL query per milestone phase (Alpha, Beta, F&F, D1PL)
- Cross-reference issue keys with the mapping file to use the correct business name (not raw Jira summary)
- If an epic listed in `Related Epics` is also a tracked issue, fetch it separately for sub-status
