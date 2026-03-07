# Example: report-config.md
#
# Copy this file to your project directory and customize it.
# The periodic-report skill will discover it automatically when you invoke /report.
#
# IMPORTANT: Do not commit internal URLs, Jira board IDs, or Slack channels
# from internal systems to public repositories.

report_type: biweekly             # biweekly | weekly | monthly | sprint

# Audience resolves to: style + template + include/exclude constraints
# Available: sprint-review-internal, executive (or any custom audience in ~/.claude/library/audiences/)
audience: sprint-review-internal

# --- DATA SOURCES ---
# Each source type maps to ~/.claude/library/sources/<type>.md
# Add or remove sources as needed. Order doesn't matter.

sources:
  # Confluence: parent page containing sprint report child pages
  - type: confluence-page
    url: https://your-instance.atlassian.net/wiki/spaces/SPACE/pages/12345/Sprint+Reports

  # Jira: milestone/epic mapping file with JQL queries
  - type: jira-sprint
    mapping: milestone_epic_mapping.md    # relative to workspace root

  # Local meeting transcripts
  - type: meeting-transcripts
    path: meetings/team-name/biweekly/   # relative to workspace root
    pattern: "YYYY_MM_DD - *.md"         # filename pattern (date prefix)

  # Google Calendar: recurring meeting with attached notes doc
  - type: google-calendar-meeting
    meeting_name: "Team Biweekly Review"  # substring match against calendar event title
    lookback_days: 14

  # Google Slides: running historical deck with date-divider slides
  # - type: google-slides
  #   presentation_url: "https://docs.google.com/presentation/d/<ID>/edit"
  #   divider_pattern: "MMM DD - MMM DD, YYYY"   # must match how dividers are titled

  # Slack channel
  # - type: slack-channel
  #   channel: "#team-updates"

  # GitHub PRs
  # - type: github-prs
  #   repo: org/repo-name
  #   state: merged                       # merged | open | all

# --- ARCHIVE ---
# Local file where all generated reports are appended (for cross-reference with previous sprint)
archive:
  path: reports/archive                   # relative to workspace root, no extension

# --- DESTINATIONS ---
# Where to publish the approved report.
# markdown-file is always written first (even if not listed here).

destinations:
  # Local markdown file (default — always included)
  - type: markdown-file
    path: report-output.md               # optional override

  # Confluence: create or update a page under the specified parent
  - type: confluence-page
    space: SPACE                         # Confluence space key
    parent: "Sprint Updates"             # parent page title

  # Slack channel
  # - type: slack-channel
  #   channel: "#team-updates"

  # Google Doc (append to existing, or create new each sprint)
  # - type: google-doc
  #   doc_id: "<existing-doc-id>"        # omit to create new doc each sprint
