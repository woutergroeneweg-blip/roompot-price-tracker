# Automation setup — Roompot Kamperland price tracker

Cursor Automations are configured in the Cursor app, **not** from a file in the
repo, so this document is the paste-ready spec. Create the automation once in
[cursor.com/automations](https://cursor.com/automations) (or the Agents window,
or via the `/automate` skill), using the settings and prompt below.

> **Prerequisite:** merge this branch to `main` first, so `requirements.txt` and
> `scripts/append_check.py` exist on the branch the automation runs against.

## Settings

| Field | Value |
|-------|-------|
| Trigger | **Scheduled**, cron `0 9 */4 * *` (every 4th calendar day at 09:00) |
| Timezone | **Europe/Amsterdam** (the timestamps logged in `prices.xlsx` use this zone) |
| Repository | `woutergroeneweg-blip/roompot-price-tracker`, branch `main` |
| Model | Any capable model with **computer use** (the price is only in the rendered page) |
| Tools | **Computer use: ON** (required to read the price). Pull request creation: optional — OFF is fine because the run commits directly. |
| Permissions | **Private** (commits as your GitHub account) or **Team Owned** (commits as `cursor`) |
| Stop condition | After **2026-11-30** the prompt self-exits; also disable the automation to stop billing runs |

`0 9 */4 * *` fires on days 1, 5, 9, 13, 17, 21, 25, 29 of each month at 09:00 —
the standard cron reading of "every 4th calendar day".

## Prompt (paste verbatim)

```
You are the "Roompot Kamperland price tracker" scheduled automation.

1. Check today's date. If it is after 2026-11-30, do nothing and stop — tracking has ended.

2. Read the CURRENT total price for the fixed booking by opening this URL in a browser
   (computer use). The price is rendered client-side, so you must use the browser, not a
   plain HTTP fetch:
   https://www.roompot.com/parks/beach-resort-kamperland/accommodations/rj-comfort#filter:eyJhIjoiMTgtMTItMjAyNiIsImQiOiIyOC0xMi0yMDI2IiwibmQiOjUsInN0Ijo5OTE0fQ==
   - Accommodation: RJ Comfort / 6-person Bungalow 6 (rjcomf), Beach Resort Kamperland.
   - Dates: 18–28 Dec 2026 (10 nights); guests: 5 (2 adults, 3 children), 0 pets; base config, no paid extras.
   - Accept the cookie banner if shown. From the price box read: the TOTAL price, the Stay/
     accommodation subtotal, and the Tourist tax. Note whether it is available or unavailable.

3. Append one row to the tracker using the repo helper (never hand-edit the xlsx):
   pip install --user --break-system-packages -r requirements.txt
   python3 scripts/append_check.py --total <TOTAL> --stay <STAY> --tax <TAX> --status <available|unavailable> --notes "scheduled automation run"
   - If unavailable: use --status unavailable and pass the most recent known total for --total
     (the helper carries the all-time low forward and marks change as "unavailable").
   - Amounts are whole euros (round if needed).

4. Commit and push the updated spreadsheet directly to main:
   git add prices.xlsx
   git commit -m "Price check: EUR <TOTAL> (<available|unavailable>) <YYYY-MM-DD>"
   git push origin main
   (If pull-request creation is enabled instead, open/update a PR to main with the same change.)

5. Reply with a one-line summary: current total, change vs the previous row, and the all-time low.
```

## Notes

- The helper derives the `change` and `lowest_total_eur_so_far` columns and keeps the
  "Total price over time" chart pointed at every row, so the prompt only needs the raw
  observed amounts.
- No email/Slack alerts are configured — review `prices.xlsx` for increases, decreases,
  and unavailability. Add the automation's "Send to Slack" tool if you want alerts.
