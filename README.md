# reporting-skill

A Claude Code skill for generating periodic reports from configurable data sources.
Composable library of sources, styles, templates, audiences, and destinations.

## Quick Start

```
/report                              # generates report for current sprint
/report Feb 23 - Mar 6              # generates report for explicit date range
"generate biweekly update"           # natural language invocation
```

## How It Works

The skill reads a `report-config.md` file from your project directory (or parent directories)
and uses it to:
1. Fetch data from configured sources (Jira, Confluence, Slack, Google Calendar, etc.)
2. Apply your audience's style and template
3. Preview the draft for your approval
4. Publish to configured destinations (local file, Confluence, Slack, Google Doc)

No config found? The skill falls back to interactive setup.

## Library Components

The skill is powered by composable library files installed at `~/.claude/library/`:

| Component | Purpose |
|-----------|---------|
| `styles/` | Communication style definitions (5 patterns) |
| `templates/` | Output structure formats |
| `sources/` | Data source adapters (Jira, Confluence, Slack, GitHub, Google) |
| `audiences/` | Audience profiles that bundle style + template + constraints |
| `destinations/` | Output adapters (markdown, Confluence, Slack, Google Doc) |

**Adding a new source type**: drop a `.md` file in `~/.claude/library/sources/` and reference `type: your-source` in your `report-config.md`. No changes to the skill needed.

## Install

**1. Clone or download this repo:**
```bash
git clone https://github.com/lucasviotti/reporting-skill.git
cd reporting-skill
```

**2. Install library files:**
```bash
mkdir -p ~/.claude/library/{styles,templates,sources,audiences,destinations}
cp -r library/styles/     ~/.claude/library/styles/
cp -r library/templates/  ~/.claude/library/templates/
cp -r library/sources/    ~/.claude/library/sources/
cp -r library/audiences/  ~/.claude/library/audiences/
cp -r library/destinations/ ~/.claude/library/destinations/
```

**3. Register the skill in `~/.claude/settings.json`:**
```json
{
  "plugins": [
    {
      "name": "periodic-report",
      "path": "/path/to/reporting-skill/SKILL.md"
    }
  ]
}
```

**4. (Optional) Install communication-style skill for better style rewrites:**
See: https://github.com/lucasviotti/communication-style

**5. Copy example config to your project and customize:**
```bash
cp examples/report-config.md your-project/report-config.md
# Edit with your sources, audience, and destinations
```

## Project Config (`report-config.md`)

Place a `report-config.md` in your project directory. The skill discovers it automatically.

```yaml
report_type: biweekly
audience: sprint-review-internal

sources:
  - type: confluence-page
    url: https://your-instance.atlassian.net/wiki/spaces/SPACE/pages/12345/Sprint+Reports
  - type: jira-sprint
    mapping: milestone_epic_mapping.md
  - type: google-calendar-meeting
    meeting_name: "Team Biweekly Review"
    lookback_days: 14

archive:
  path: path/to/archive-file

destinations:
  - type: confluence-page
    space: MYSPACE
    parent: "Sprint Updates"
  - type: slack-channel
    channel: "#team-updates"
```

See `examples/report-config.md` for a complete annotated example.

## Styles

| Style | Use for |
|-------|---------|
| `concise-professional` | Internal Slack, sprint updates, cross-team coordination |
| `leadership-polite` | Requests to teams, diplomatic asks |
| `executive-summary` | C-level updates, escalations |
| `external-professional` | Vendor emails, recruiter responses |
| `technical-product-discussion` | Engineering discussions, design decisions |

Styles are defined in `library/styles/`. Add your own by creating a new `.md` file there.

## Repo Structure

```
reporting-skill/
├── SKILL.md                          ← Main periodic-report skill
├── README.md
├── library/
│   ├── styles/                       ← Communication style definitions
│   ├── templates/                    ← Output structure formats
│   ├── sources/                      ← Data source adapters
│   ├── audiences/                    ← Audience profiles
│   └── destinations/                 ← Output destination adapters
└── examples/
    └── report-config.md              ← Annotated example config
```

## GitHub

https://github.com/lucasviotti/reporting-skill
