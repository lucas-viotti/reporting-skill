# Destination: Slack Channel

Posts the report as a Slack message to a channel or DM.

---

## MCP Tool

`mcp__plugin_slack_slack__slack_send_message`

---

## Config Fields

```yaml
- type: slack-channel
  channel: "#team-updates"          # channel name or user ID for DM
```

---

## Write Instructions

1. Format the report for Slack:
   - Remove markdown headers (`###`) — use `*Bold text*` for section labels instead
   - Replace `**Bold**` with `*Bold*` (Slack mrkdwn)
   - Keep bullet points as `-` prefixed lines
   - Add a sprint date header line at the top: `*Sprint <DATE RANGE>*`
   - Keep the message under ~2000 characters; truncate with "See full report at [link]" if needed
2. Call `mcp__plugin_slack_slack__slack_send_message` with:
   - `channel`: from config
   - `text`: formatted message

---

## Slack Format Example

```
*Sprint Feb 23 - Mar 6, 2026*

Feature A delivery delayed (see risks below)

*Feature A*: Design finalized; implementation in progress.
*Feature B*: Integration complete; validation starting next sprint.
*Feature C*: Core capability delivered; end-to-end testing underway.

*Challenges & Path to Green*
- Feature A at risk for target date. Revised target: end of next sprint (medium confidence).
- Path to Green: Updated roadmap publishing this week.
```

---

## Notes

- Slack format uses `*bold*` not `**bold**` (single asterisk)
- Do not use markdown heading syntax (`#`) in Slack — it does not render
- Always confirm with the user before posting to a public channel
- During initial setup, fetch example messages from the target channel to match the expected format and tone
