---
name: one-on-one-notes
description: Generate structured 1:1 notes from a transcript file. Use when the user says "generate 1:1 notes for [path]", "give me 1:1 notes for [path]", "1:1 notes for [path]", or any variation asking to summarize or format a 1:1 meeting transcript.
---

# 1:1 Notes Generator

Read a 1:1 meeting transcript from a file path and produce clean, structured notes in a standard format.

## Step 1: Read the Transcript

Read the file at the path provided by the user. Accept any format: plain text, markdown, auto-generated Zoom/Meet transcript, or rough notes.

## Step 2: Extract the Date

- Look for a date in the transcript (in the filename, header, or body).
- If no date is found, use today's date in `MM-DD-YYYY` format.

## Step 3: Generate the Notes

Produce output in exactly this format:

```markdown
[date]

## Discussion Topics & Key Points

### TL;DR

[2-4 sentence high-level summary of what was discussed and any notable outcomes or themes.]

### [Topic Name]

* [Key point or note]
* [Key point or note]

### [Topic Name]

* [Key point or note]
* [Key point or note]

## Action Items

* [Owner if identifiable]: [action]
* [action]
```

## Formatting Rules

- **Date**: Use the format `YYYY-MM-DD`. Pull from transcript if available, otherwise use today's date.
- **TL;DR**: 2-4 sentences max. Capture the overall tone and any big decisions or themes. No bullet points here — prose only.
- **Topics**: Group related discussion points into named sections. Derive topic names from the conversation — don't use generic names like "Topic 1". Aim for 2-6 topics depending on the length of the meeting.
- **Notes under topics**: Concise bullets. Capture decisions, concerns, context, and notable quotes. Strip filler and crosstalk.
- **Action items**: One bullet per action. Prefix with owner name if clear from context (e.g. `* Lindsey: follow up with Ari on timeline`). If owner is ambiguous, just list the action.
- **Tone**: Professional but not stiff. Match the register of the conversation.

## Gotchas

- **Auto-generated transcripts** often have speaker labels like "Speaker 1:" or names — use them to attribute action items where possible.
- **Zoom transcripts** include timestamps — ignore them for content, but use any date metadata for the note header.
- **If the transcript is very short** (under ~5 exchanges), the TL;DR may be sufficient with minimal topic sections — don't pad unnecessarily.
- **Do not invent content** — only summarize what's actually in the transcript. If something is unclear, note it as "[unclear]" rather than guessing.
- **Skip small talk** — opening pleasantries and closing remarks don't need to be topics unless they contain substantive content.

## Output

Write the notes to `artifacts/1on1-[date]-[participant-name-if-detectable].md` and render:

```bash
bin/render artifacts/1on1-[date]-[participant-name-if-detectable].md
```

If no participant name is detectable from the transcript, use `artifacts/1on1-[date].md`.

Also print the notes directly to the conversation so the user can see them immediately.
