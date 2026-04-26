# skills

Personal skills that I find helpful.

## Installation

### For GitHub Copilot

1. Open VS Code Settings (Cmd/Ctrl + ,)
2. Search for "Copilot Skill Library Path"
3. Add the path to this skills folder

Or add to your VS Code settings.json:

```json
{
  "github.copilot.chat.skillLibrary.folders": ["/path/to/skills"]
}
```

### For Claude Desktop

1. Open Claude Desktop settings
2. Navigate to Developer → Edit Config
3. Add a filesystem MCP server pointing to this skills directory:

```json
{
  "mcpServers": {
    "filesystem": {
      "command": "npx",
      "args": [
        "-y",
        "@modelcontextprotocol/server-filesystem",
        "/path/to/skills"
      ]
    }
  }
}
```

4. Restart Claude Desktop

## Available Skills

### Daily Standup

**Triggers**: "give me my standup for today", "what am I doing today", "what are my tasks today"

**Usage**: Ask your AI assistant to generate your standup/daily summary

Generates an emoji-prefixed list of today's meetings and tasks based on your calendar and recent Slack messages. Perfect for posting to Slack standups.

**Example output**:

```
  :brain: Agentic Onboarding Planning: Part 2
  :building_construction: Foundation Sync
  :bust_in_silhouette: 1:1 Cody
  :green_salad: Lunch
  :bar_chart: Sample Data Metrics/Tracking
```

### One-on-One Notes

**Triggers**: "generate 1:1 notes for [path]", "give me 1:1 notes for [path]"

**Usage**: Ask your AI assistant to generate 1:1 notes from a transcript file

Reads a meeting transcript file and produces structured notes with:

- TL;DR summary
- Discussion topics with key points
- Action items with owners

### Schedule Lunches

**Triggers**: "schedule my lunches", "schedule my lunch for the month"

**Usage**: Ask your AI assistant to schedule your monthly lunches

Automatically finds open 30-minute windows between 12-3pm on remaining weekdays this month and creates "Lunch" calendar blocks where none exist.

### Write Skill

**Triggers**: "create a skill for [task]", "write a new skill", "make a skill that does [x]"

**Usage**: Ask your AI assistant to create a new skill definition

Generates a properly structured SKILL.md file with frontmatter, step-by-step instructions, output format, and gotchas. Perfect for expanding your skill library with new capabilities.
