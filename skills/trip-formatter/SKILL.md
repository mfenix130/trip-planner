# Trip Formatter Skill

Convert the completed itinerary into an Excel-compatible CSV file. The Markdown table in `itinerary.md` stays as-is; this skill writes the CSV version only.

---

## Runtime Rules

- Always read the current `itinerary.md` — do not use a cached version.
- The CSV must contain exactly the same data as the MD table. Do not add or remove rows.
- Escape CSV correctly: wrap any cell containing commas, quotes, or newlines in double quotes. Escape internal double quotes as `""`.
- Maps links in CSV should be the full URL string (not Markdown `[Maps](URL)` syntax) — spreadsheets will auto-link them.
- Preserve emoji (⚠) in the CSV — they render in Excel and Sheets.

---

## Purpose

Produce a spreadsheet-ready CSV so the user can open the itinerary in Excel or Google Sheets, sort by day, filter by category, colour-code, and share with travel companions.

---

## Inputs

Required:
- `itinerary.md` — fully enriched itinerary with Maps links (output of maps-enricher)

---

## CSV Schema

The CSV columns match the MD table exactly:

```
Day,Date,Time,Place,Category,Famous For,Hours,Visit Duration,Travel to Next,Maps Link,Tips
```

**Column rules:**

| Column | Format | Notes |
|--------|--------|-------|
| Day | `Day 1`, `Day 2`, etc. | Consistent casing |
| Date | `Mon 14 Apr` or blank if no start date | Blank if not provided |
| Time | `9:00 AM` | 12-hour format with AM/PM |
| Place | Plain text | No markdown formatting |
| Category | Plain text | Monument, Museum, Neighborhood, Park, etc. |
| Famous For | Plain text | 1–2 sentences; remove any markdown |
| Hours | Plain text | e.g. `9am–7pm (Apr–Sep)` or `All day` |
| Visit Duration | Plain text | e.g. `2–3 hrs` |
| Travel to Next | Plain text | e.g. `15 min walk`, `25 min metro` — strip the `→` arrow |
| Maps Link | Full URL | e.g. `https://www.google.com/maps/place/...` — not markdown link syntax |
| Tips | Plain text | Remove ⚠ prefix from text but keep the emoji if present |

**Special rows:**

Travel legs (e.g. train between cities) should be included as their own rows:
```
Day 3,,All day,Train: Rome → Florence,Travel,High-speed rail connection between cities,,3.5 hrs (incl. station time),—,https://www.google.com/maps/dir/Rome+Italy/Florence+Italy,Book via Trenitalia; departs Roma Termini
```

---

## Steps

### Step 1: Parse itinerary.md

- Read the Markdown table between `## Day-by-Day Schedule` and `## Day Summaries`
- For each row, extract all 11 column values
- Convert Markdown link `[Maps](URL)` to raw URL string
- Strip any remaining Markdown formatting (bold `**`, italic `*`, backticks)

### Step 2: Write the CSV

- First row: header row with column names
- Subsequent rows: one per itinerary entry
- Encoding: UTF-8 (preserves emoji and special characters)
- Line endings: standard (LF)

### Step 3: Add a Summary Sheet Block

After the main table, add a blank row, then a summary block so the CSV opens with useful context at the bottom:

```
,,,,,,,,,,
TRIP SUMMARY,,,,,,,,,,
Trip Name,[trip-name],,,,,,,,
Destinations,[city1; city2; city3],,,,,,,,
Total Days,[N],,,,,,,,
Pace,[pace],,,,,,,,
Generated,[date],,,,,,,,
,,,,,,,,,,
BOOKING REQUIRED,,,,,,,,,,
Place,How to Book,,,,,,,,,
[place],[booking note],,,,,,,,,
```

---

## Output

Write one file: `itinerary.csv`

Do not modify `itinerary.md`.

After writing, confirm the row count: `Written itinerary.csv — [N] itinerary rows + header + summary block.`
