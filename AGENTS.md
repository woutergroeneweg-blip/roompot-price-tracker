# AGENTS.md

## Cursor Cloud specific instructions

### What this repo is
A price tracker for a single fixed Roompot booking. There is no long-running
service or web app: the "application" is a Cursor Automation that periodically
reads the booking total from the Roompot URL in `README.md` and appends a row
to `prices.xlsx` (sheet `Price History`), which also has a `Summary` sheet and a
"Total price over time" line chart. See `README.md` for the booking details.

### Environment
- Python 3.12 (system interpreter). Dependencies are installed via the startup
  update script (`pip install --user --break-system-packages -r requirements.txt`),
  so `import openpyxl` / `import requests` work with the system `python3` directly
  — no virtualenv activation is needed. `python3 -m venv` is NOT available out of
  the box (needs the `python3.12-venv` apt package), so prefer the user install.
- Network egress is open; the tracked Roompot page returns HTTP 200.

### Running / testing the tracker
- Append a price check: `python3 scripts/append_check.py --total <EUR> [--stay N --tax N --status available|unavailable --notes "..."] [--file prices.xlsx]`.
  It derives the `change` and `lowest_total_eur_so_far` columns and keeps the
  chart data range pointing at all rows.
- Lint/syntax check: `python3 -m py_compile scripts/append_check.py` (there is no
  configured linter/test suite in this repo).
- When testing the append flow, run against a COPY (e.g. `cp prices.xlsx /tmp/demo.xlsx`
  then pass `--file /tmp/demo.xlsx`) so you do not commit throwaway price rows into
  the real history.

### Gotchas
- `openpyxl` does round-trip the existing chart, the two sheets, and the `Summary`
  formulas correctly on load/save (verified), but it does NOT evaluate formulas —
  the `Summary` cells show their computed values only when opened in a real
  spreadsheet app (Excel / LibreOffice), which is not installed here.
- The Roompot booking total is rendered client-side, so a plain `requests.get`
  of the page does not contain the price; the automation supplies the observed
  total to `scripts/append_check.py` rather than scraping it from static HTML.
