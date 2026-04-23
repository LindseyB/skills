---
name: daily-standup
description: Generate a simple emoji-prefixed list of today's meetings and tasks based on the calendar and recent Slack messages. Use when the user says "give me my standup for today", "what am I doing today", "what are my tasks today", "give me my todos today", or any similar phrasing asking for a daily overview.
---

# Daily Standup Generator

Produce a clean, emoji-prefixed list of what the user is doing today — no bullets, no extra formatting, just the list. Modeled after the format the user already uses in their own standup posts.

## Step 1: Fetch Today's Calendar

```bash
./ld gcal today --output json
```

Filter out:
- All-day events (OOO, PTO banners, holidays)
- Events the user has **declined**
- Events marked as "free" / non-blocking
- Duplicate entries

Keep events in **chronological order**.

## Step 2: Fetch Today's Slack Context (Optional Enrichment)

Pull recent Slack messages to surface any async tasks or follow-ups the user mentioned they'd do today:

```bash
./ld slack search "from:lbieda after:yesterday" --limit 20 --sort timestamp
```

Scan for:
- Commitments like "I'll do X today", "will follow up", "on my list for today"
- Action items assigned to the user in threads they're active in

Only include Slack-sourced items if they are clearly concrete tasks for today. Do not add speculative or vague items.

## Step 3: Build the List

Combine calendar events (in time order) and any Slack-sourced tasks into a single ordered list.

For each item, assign an emoji and write a short, clean title:

### Emoji Mapping

| Event type | Emoji |
|---|---|
| 1:1 / one-on-one | `:bust_in_silhouette:` |
| Team sync / standup / foundation sync | `:building_construction:` |
| Planning session / roadmap / strategy | `:cinna-frolic:` |
| Engineering / technical work / coding | `:coding:` |
| Metrics / data / analytics / tracking | `:bar_chart:` |
| Lunch / food / break | `:green_salad:` |
| Incident / on-call / fire | `:fire_engine:` |
| Interview | `:microphone:` |
| All-hands / company meeting | `:busts_in_silhouette:` |
| Triad / cross-functional / leadership | `:small_red_triangle:` |
| AI / agent / ML work | `:robot_face:` |
| Design / UX / review | `:art:` |
| Writing / docs / PRD | `:kirby-type:` |
| Hiring / recruiting | `:briefcase:` |
| Demo / presentation | `:clapper:` |
| Offsite / travel | `:airplane:` |
| Anything else / uncategorized | `:calendar:` |

### Title Formatting Rules

- Keep titles **short** — 3-6 words max
- Strip filler words like "Weekly", "Sync with", "Meeting:", "Discussion about"
- For 1:1s: use format `1:1 [First Name]` — just the first name
- For recurring standups: just the team or project name (e.g. `Foundation Sync` not `Foundation Weekly Team Standup`)
- Capitalize each word
- No punctuation at the end

## Step 4: Output

Print the list with **no bullets, no headers, no extra formatting** — just one item per line, emoji followed by a space and the title, indented with two spaces on all items:

```
:brain: Agentic Onboarding Planning: Part 2
  :building_construction: Foundation Sync
  :bust_in_silhouette: 1:1 Cody
  :bust_in_silhouette: 1:1 Alexis
  :wrench: Quickstart Wizard CLI Followup
  :green_salad: Lunch
  :bust_in_silhouette: 1:1 Eric
  :bust_in_silhouette: 1:1 Patrick
  :bar_chart: Sample Data Metrics/Tracking
  :small_red_triangle: Foundation Triad
  :robot_face: Agentic Work Planning
```

Output this directly in the conversation — no artifact file needed. The user will copy-paste it into Slack.

## Gotchas

- **Declined events**: Skip them. Don't include anything the user said no to.
- **Lunch**: If there's a "Lunch" block on the calendar, include it. If not, do **not** add a fabricated lunch unless the user explicitly asked.
- **All-day events**: Skip banners like "Cody OOO", "PTO", holidays. They are not tasks.
- **Duplicates**: If the same meeting appears twice (e.g. from two calendars), deduplicate.
- **Optional events**: Include events marked optional unless they are declined — the user can decide to attend.
- **Long event titles**: Shorten aggressively. A title like "Agentic Experiences V2 Weekly Planning — Foundation Team" becomes `Agentic Onboarding Planning`.
- **No Slack items if nothing clear**: If Slack doesn't surface any concrete today-tasks, that's fine — just use the calendar. Don't add noise.
