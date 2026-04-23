---
name: schedule-lunches
description: Schedule monthly lunch blocks on Google Calendar. Use when the user says "schedule my lunches", "schedule my lunch for the month", "monthly lunches", or any variation asking to block lunch time on their calendar.
---

# Schedule Monthly Lunches

Find open 30-minute windows between 12:00pm and 3:00pm on weekdays for the current month and create "Lunch" events on Google Calendar for any day that doesn't already have one.

## Goal

Block a 30-minute lunch on every remaining weekday this month where lunch isn't already covered. The user should be able to invoke this once and have their month sorted.

## Step 1: Determine Date Range

Compute:
- `$TODAY` — today's date (YYYY-MM-DD)
- `$MONTH_END` — last day of the current month (YYYY-MM-DD)

Use today as the range start (don't backfill past days). If today is already the last day of the month, let the user know and stop.

## Step 2: Fetch the Month's Events

```bash
./ld gcal range $TODAY $MONTH_END --output json
```

Parse the results to build a list of all weekdays (Mon-Fri) from today through end of month.

## Step 3: For Each Weekday, Check for Lunch Coverage

For each weekday in the range:

1. **Check if lunch is already covered** — scan events on that day for anything that:
   - Is titled "Lunch", "lunch", or contains "lunch" (case-insensitive)
   - OR is a non-declined event with any overlap in the 12:00pm–3:00pm window that lasts 30+ minutes and appears to be a break/personal block

2. **If lunch is already covered** — skip that day.

3. **If no lunch found** — check free/busy to find an open 30-minute slot:

```bash
./ld gcal freebusy lbieda@launchdarkly.com $DATE"T12:00" $DATE"T15:00" --output json
```

   Walk through the 12:00-3:00pm window in 30-minute increments (12:00, 12:30, 1:00, 1:30, 2:00, 2:30) and find the first slot with no busy overlap.

4. **If a free slot is found** — create the event:

```bash
./ld gcal create "Lunch" $DATE"T"$SLOT_START $DATE"T"$SLOT_END --no-zoom --description "Lunch block"
```

5. **If no free slot exists between 12-3pm** — note that day as "no available slot" and move on. Do not create an event.

## Step 4: Report Results

After processing all days, print a summary:

```
Lunch blocks scheduled:

  Thu Apr 24  12:00–12:30
  Fri Apr 25  12:30–1:00
  Mon Apr 28  12:00–12:30
  ...

Already had lunch:
  Wed Apr 23

No slot available (12-3pm fully booked):
  Tue Apr 29
```

Keep it concise. If everything was already covered, say so.

## Gotchas

- **Declined events don't block time** — ignore any event where the user's RSVP is declined when checking for busy time.
- **All-day events** — skip all-day events when checking for lunch coverage (they don't block the calendar slot).
- **Weekends** — skip Saturday and Sunday entirely.
- **Today** — include today only if it's before 12pm in the user's timezone (PT). If it's already past 3pm, skip today.
- **No Zoom** — always pass `--no-zoom` when creating lunch events; no meeting link needed.
- **Prefer earlier slots** — start scanning from 12:00 and take the first available window. Don't optimize for "best" time, just find the first open 30 minutes.
- **Month boundary** — if invoked in the last few days of the month with nothing left to schedule, offer to schedule next month instead.
