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

```bash
git clone https://github.com/lucas-viotti/reporting-skill.git
mkdir -p ~/.claude/library/{styles,templates,sources,audiences,destinations}
cp -r library/styles/       ~/.claude/library/styles/
cp -r library/templates/    ~/.claude/library/templates/
cp -r library/sources/      ~/.claude/library/sources/
cp -r library/audiences/    ~/.claude/library/audiences/
cp -r library/destinations/ ~/.claude/library/destinations/
```

Then register the skill — see [Claude Code plugin docs](https://code.claude.com/docs/en/plugins).

**(Optional) Install communication-style for better style rewrites:**
https://github.com/lucas-viotti/communication-style

## Project Config — Required for real use

> **The skill needs a `report-config.md` in your project to know where to pull data and where to publish.** Without it, the skill falls back to interactive mode — functional, but you'll be asked the same questions every time.

**Recommended: run `/new-report` to set up interactively.** The command asks you questions and writes the config file (and a dedicated slash command) for you.

Or create `report-config.md` manually in your project root:

```yaml
report_type: biweekly
audience: sprint-review-internal

sources:
  - type: jira-sprint
    mapping: milestone_epic_mapping.md
  - type: confluence-page
    url: https://your-instance.atlassian.net/wiki/spaces/SPACE/pages/12345/
  - type: google-calendar-meeting
    meeting_name: "Team Biweekly Review"
    lookback_days: 14

archive:
  path: reports/archive

destinations:
  - type: markdown-file
    path: report-output.md
  - type: confluence-page
    space: MYSPACE
    parent: "Sprint Updates"
  - type: slack-channel
    channel: "#team-updates"
```

See `examples/report-config.md` for a fully annotated version with all available options.

The skill discovers `report-config.md` by searching from the current directory upward — place it at the project root and it works from any subdirectory.

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
├── commands/
│   └── new-report.md                 ← /new-report guided setup wizard
├── references/
│   └── report-command-template.md   ← Template for generated per-report commands
└── examples/
    └── report-config.md              ← Annotated example config
```

## GitHub

https://github.com/lucas-viotti/reporting-skill
