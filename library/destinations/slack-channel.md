# Destination: Slack Channel

Posts the report as a Slack message to a channel or DM.

---

## MCP Tool

`mcp__plugin_slack_slack__slack_send_message`

---

## Config Fields

```yaml
- type: slack-channel
  channel: "#da-execution-review"   # channel name or user ID for DM
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

Beta testing for revolving and lateness is delayed (see risks below)

*Revolving*: Completed design for change state and multiple charges; implementation in progress.
*Lateness*: Design finalized; implementation starting next sprint.
*Close complete bill*: Minimum payment warning ready for integrations.
*Reversals*: Authorization reversal complete; confirmations in progress.

*Challenges & Path to Green*
- Beta milestone at risk for early April. Early May is the working target (medium confidence).
- Path to Green: Updated roadmap publishing Feb 27.
```

---

## Notes

- Slack format uses `*bold*` not `**bold**` (single asterisk)
- Do not use markdown heading syntax (`#`) in Slack — it does not render
- Always confirm with the user before posting to a public channel
- During initial setup, fetch example messages from the target channel to match the expected format and tone
