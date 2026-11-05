---
name: one-on-one-notes
description: Generate structured 1:1 notes from a transcript file. Use when the user says "generate 1:1 notes for [path]", "give me 1:1 notes for [path]", "1:1 notes for [path]", or any variation asking to summarize or format a 1:1 meeting transcript.
---

# 1:1 Notes Generator

Read a 1:1 meeting transcript from a file path and produce clean, structured notes ready to paste as a new entry at the top of that person's page in the "Lindsey 1:1s" Confluence space (`L1`).

## Step 1: Read the Transcript

Read the file at the path provided by the user. Accept any format: plain text, markdown, auto-generated Zoom/Meet transcript, or rough notes. If the folder holds two transcript exports, use the later, larger one.

## Step 2: Extract the Date

Look for a date in the transcript (in the filename, header, or body). If no date is found, use today's date.

## Step 3: Generate the Notes

Produce output in exactly this format:

```markdown
[DD Mon YYYY]

### TL;DR

[2-4 sentence high-level summary of what was discussed and any notable outcomes or themes.]

### [Topic Name]

* [Key point or note]
* [Key point or note]
  * [Supporting detail or specific example]

### [Topic Name]

* [Key point or note]
```

Nothing goes above the date and nothing follows the last topic: no title or participant heading, no `## Discussion Topics` wrapper, no Action Items section. The first 1:1 (17 Aug 2026) had a trailing Action Items section; every entry since dropped it.

## Formatting Rules

- **Date**: `DD Mon YYYY` (e.g. `05 Nov 2026`) on its own line as the first line.
- **TL;DR**: 2-4 sentences max. Capture the overall tone and any big decisions or themes. Prose only, no bullets.
- **Topics**: Group related discussion points into named sections. Derive topic names from the conversation, don't use generic names like "Topic 1". Published entries run 4-6 topics; go shorter for short meetings, never pad.
- **Notes under topics**: Concise bullets. Capture decisions, concerns, context, and notable quotes. Strip filler and crosstalk. Sub-bullets are fine for lists of specifics.
- **Actions**: Record each action inline, in the topic where it arose, as prose: `Lindsey will share the board link` or `Lindsey to send Christina small tickets`. If the owner isn't clear, phrase it without one.
- **Attribution**: A `Name:` prefix marks whose view or statement a bullet is (`* Lindsey: Christina is doing great`). Don't use it for actions.
- **Emphasis**: No bold in the body. Bullets are plain text.
- **Tone**: Professional but not stiff. Match the register of the conversation.

## Gotchas

- **Auto-generated transcripts** often have speaker labels like "Speaker 1:" or names. Use them to attribute actions and statements correctly.
- **Zoom transcripts** include timestamps. Ignore them for content, but use any date metadata for the note header.
- **Small talk**: skip opening pleasantries and closing remarks. A longer side conversation that reveals interests or working style can stay as a short topic at your judgment; it is occasional, not the default.
- **If the transcript is very short** (under ~5 exchanges), the TL;DR may be sufficient with one or two topics.
- **Do not invent content**. Only summarize what's actually in the transcript. If something is unclear, note it as "[unclear]" rather than guessing.

## Output

Write the notes to `artifacts/1on1-[YYYY-MM-DD]-[participant-name-if-detectable].md` (ISO date in the filename, `DD Mon YYYY` only inside the note) and render:

```bash
bin/render artifacts/1on1-[YYYY-MM-DD]-[participant-name-if-detectable].md
```

If no participant name is detectable from the transcript, use `artifacts/1on1-[YYYY-MM-DD].md`.

Also print the notes directly to the conversation so the user can see them immediately.

Do not post to Confluence. The user pastes the entry into the page themselves.
