---
name: daily-standup
description: Generate Lindsey's Geekbot standup answers ("What will you do today?" and "What have you done since yesterday?") as grouped, cinnamoroll-emoji-prefixed lines built from the daily log, calendar, and Slack. Use when the user says "give me my standup", "write my standup for today", "standup for what I did yesterday", "what am I doing today", "give me my todos today", or any similar phrasing asking for a daily overview or Geekbot answer.
---

# Daily Standup Generator

Produce paste-ready answers for the Foundation Standup in Geekbot: one line per group, each line starting with a cinnamoroll Slack emoji. No bullets, no headers, no leading spaces.

## Which answer to write

| User asks for | Answer | Day it covers |
|---|---|---|
| "today", "what will I do", "my todos" | **What will you do today?** | Today |
| "yesterday", "what did I do", "since Friday" | **What have you done since yesterday?** | The previous workday (Friday on a Monday) |
| "my standup" with no qualifier | Both, labeled | Both |

## Step 1: Load the live cinnamoroll emoji set

Every run, list what actually exists in the workspace, since custom emoji get added and removed:

```
slack_search_emojis(query: "cinnamoroll,cinna")
```

Only output emoji names that appear in that result, plus the two non-cinnamoroll exceptions below. Never invent a cinnamoroll name.

## Step 2: Gather sources

1. **Daily log**: `~/Documents/main/logs/YYYY-MM-DD.md` for the day being covered. It's the best source: Meetings, Meeting Notes (skipped or moved meetings are marked there), Slack, GitHub, Incidents, Notes, and Carry-over.
2. **Calendar** (fallback for meetings, or when there's no log yet): `./ld gcal today --output json`, or `./ld gcal date YYYY-MM-DD` for a past day. Keep only events the user **accepted** or organizes. Skip declined and needsAction ones, plus all-day banners, holidays, OOO, and "No meeting Wednesday".
3. **Slack** (for "yesterday", or when the log is thin): `./ld slack search "from:@lbieda on:YYYY-MM-DD" --limit 100 --output json`.
4. **Previous answers** (for tone): `./ld slack history D0AAWSQ26MR --limit 20 --output json` is the Geekbot DM. Match its style.

## Step 3: Group and pick emoji

Group same-type items onto one line and join names with commas, for example `:cinnamoroll-1on1: 1:1s with Ari, Henry, Natalie`. Order the lines: on-call first, then meetings in roughly calendar order, then heads-down work last.

### Preferred emoji (the user's established usage)

| Item | Emoji | Example line |
|---|---|---|
| On-call / IM week | `:incident-io:` | `:incident-io: On-call this week` |
| 1:1s | `:cinnamoroll-1on1:` | `:cinnamoroll-1on1: 1:1s with Ari, Natalie` |
| Team sync / planning | `:cinna-frolic:` | `:cinna-frolic: Foundation sync` |
| Cross-team meetings, reviews, SERs | `:cinnamoroll-zoom:` | `:cinnamoroll-zoom: Eng Managers Weekly, SER` |
| Foundation Triad | `:triadlove:` | `:triadlove: Foundation Triad` |
| Tech Team Weekly / all hands | `:cinnamoroll-with-balloons:` | `:cinnamoroll-with-balloons: Tech Team Weekly Meeting` |
| Heads-down work (OKRs, check-ins, docs) | `:cinnamoroll-teacup:` | `:cinnamoroll-teacup: Scoring OKRs and working on FY27Q3 check-ins` |
| Shipped / merged / closed out | `:cinnamoroll-ok:` | `:cinnamoroll-ok: Merged the INC-301 hardening PR` |
| Investigated / dug into / answered | `:cinnamoroll-question:` | `:cinnamoroll-question: Dug into a customer's account lockout` |
| Travel | `:cinnamoroll-in-a-plane:` | |
| Celebration / launch | `:cinnamoroll-cheerful:` | |

`:incident-io:` and `:triadlove:` are the only non-cinnamoroll emoji allowed.

**Fallback:** if an item fits no row, or a preferred emoji is missing from Step 1's result, pick the closest cinnamoroll emoji by name from the live list. For example, `-musicnote` for something music-related, `-sk8ing` for something fast or unblocked, `-blanket` or `-peaceful` for low-key work. Never fall back to standard Unicode emoji.

## Step 4: Write the lines

- **Today:** accepted meetings, plus at most two concrete heads-down tasks from the log's Notes or Carry-over (due dates first). Leave out ongoing habits.
- **Yesterday:** what actually happened, from the log's Slack, GitHub, Incidents, and Meeting Notes. Lead with outcomes ("resolved INC-300", "merged …", "escalated … to Billing"). Leave out meetings that were skipped or moved. Aim for 3 to 6 lines.
- Keep each line short, with names only (first names for people). Leave out sensitive details (security incident specifics beyond the INC number and a short title, personal or family matters, performance topics).
- If the covered day was a day off with nothing logged, suggest a single `:cinnamoroll-sleep:` instead.

## Step 5: Output

Put each answer in its own fenced block, **flush left with no leading spaces**, labeled with the Geekbot question:

**What will you do today?**
```
:incident-io: On-call this week
:cinnamoroll-zoom: Billing <> Foundation chat, Context kinds problem framing and scoping
:cinnamoroll-1on1: 1:1s with Ari, Natalie
:triadlove: Foundation Triad
:cinnamoroll-teacup: FY27Q3 check-ins and getting team work into Atlas
```

After the block, add one line naming any emoji that came from the fallback, so the user can swap it. For "How do you feel today?", suggest one emoji from the live set if asked; the user's usual answers are `:cinnamoroll-sleep:` and `:cinnamoroll-rip:`.

## Gotchas

- **Never post to Geekbot or Slack.** The user pastes the answers themselves.
- **Skipped or moved meetings:** the log marks them ("skipped", "moved"). Leave them out of "yesterday".
- **Lunch:** leave it out. The user's standups never include it.
- **Unresolved Slack IDs:** don't guess names. Leave that item out or describe it without a name.
- **Duplicates:** deduplicate the same meeting showing up from two calendars.

