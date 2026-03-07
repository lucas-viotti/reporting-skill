# Source: GitHub PRs

Fetches merged or open pull requests from a GitHub repository to extract delivery activity.

---

## MCP Tool

`mcp__plugin_github_github__list_pull_requests`

For individual PR details:
`mcp__plugin_github_github__get_commit`

---

## Parameters

- `owner`: GitHub org or user (from `repo` config field, before `/`)
- `repo`: repository name (from `repo` config field, after `/`)
- `state`: from config (`merged`, `open`, or `all` — default `merged`)
- `base`: default branch (usually `main`)

---

## Config Fields

```yaml
- type: github-prs
  repo: org/repo-name
  state: merged          # merged | open | all
```

---

## Extraction Instructions

1. Fetch PRs merged (or open, per config) within the sprint date range
2. For each PR:
   - Title: extract the feature or fix being delivered
   - Labels: use to categorize (feature, bugfix, milestone tag)
   - Merged date: use as delivery date

**Extract:**
- Feature delivery: PRs titled with milestone or capability names
- Bug fixes with milestone impact
- Author as owner

**Ignore:**
- Chore, docs, or dependency update PRs (typically)
- Draft PRs
- PRs with no milestone-relevant keywords in title

**Output shape:**
```
{ topic: "<capability or milestone from PR title>", status: "delivered", owner: "<PR author>", date: "<merged date>", source: "github", pr_number: <N> }
```

---

## Notes

- GitHub PRs are most useful as a technical signal source — translate to business outcomes before including in reports
- If PR titles are purely technical, use the PR body/description for business context
- This source is optional — skip if the project does not use GitHub or PR activity is already captured via Jira
