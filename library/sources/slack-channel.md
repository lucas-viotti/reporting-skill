# Source: Slack Channel

Reads recent messages and threads from a Slack channel to extract status updates and decisions.

---

## MCP Tool

`mcp__plugin_slack_slack__slack_read_channel`

For reading specific threads:
`mcp__plugin_slack_slack__slack_read_thread`

---

## Parameters

- `channel_name` or `channel_id`: from `channel` config field
- `oldest`: sprint start date (Unix timestamp)
- `latest`: sprint end date (Unix timestamp)
- `limit`: 100

---

## Config Fields

```yaml
- type: slack-channel
  channel: "#team-updates"
```

---

## Extraction Instructions

1. Fetch messages from the channel within the sprint date range
2. For each message:
   - Identify milestone or project mentions
   - Extract status signals: completed, blocked, at risk, launched
   - Note the author as potential owner
3. For threads with significant replies (>3 replies), fetch and read the thread

**Extract:**
- Milestone/project status updates
- Decisions announced in the channel
- Blockers or escalations flagged
- Links to Jira issues or Confluence pages (note them, don't fetch unless needed)

**Ignore:**
- Bot messages and automated notifications
- Emoji reactions without context
- Off-topic conversation
- Routine "standup" check-ins without substantive content

**Output shape:**
```
{ topic: "<mentioned milestone or topic>", status: "<update text>", owner: "<author>", date: "<message date>", source: "slack" }
```

---

## Notes

- Slack is often a supplementary source — use to catch items not in Jira or Confluence
- If the channel has low signal-to-noise, only surface messages with explicit milestone names
- Thread reads are optional — fetch only if the top-level message indicates a decision or risk
