# Roompot Kamperland price tracker

Tracks the **total price** for a fixed Roompot booking and logs history in `prices.xlsx`.

## Booking

| Field | Value |
|-------|--------|
| Park | Beach Resort Kamperland |
| Unit | RJ Comfort / 6-person Bungalow 6 (`rjcomf`) |
| Dates | 18–28 Dec 2026 (10 nights) |
| Guests | 5 persons (2 adults, 3 children), 0 pets |
| Config | Base only — no paid extras |

URL: https://www.roompot.com/parks/beach-resort-kamperland/accommodations/rj-comfort#filter:eyJhIjoiMTgtMTItMjAyNiIsImQiOiIyOC0xMi0yMDI2IiwibmQiOjUsInN0Ijo5OTE0fQ==

## Spreadsheet

Open `prices.xlsx`:

- **Price History** — one row per check (timestamp, total, stay, tax, status, change, running low)
- **Summary** — booking details plus latest total / all-time low formulas
- Line chart on Price History for totals over time

## Automation

Cursor Automation **Roompot Kamperland price tracker** runs every 4th calendar day at 09:00, scrapes the URL above, appends a row, and commits. Tracking stops after **2026-11-30** (or when you disable the automation).

See [`AUTOMATION.md`](AUTOMATION.md) for the ready-to-paste schedule, settings, and agent prompt used to set this automation up in Cursor.

No email/Slack alerts — check this Excel file for increases, decreases, and unavailability.
