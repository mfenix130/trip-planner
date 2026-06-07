# trip-planner

Claude agent and skills for building a detailed day-by-day travel itinerary. Give it your destinations, number of days, and pace — it researches tourist spots, schedules them intelligently, adds Google Maps links, and exports a spreadsheet-ready CSV.

---

## Getting Started

**Requirements:** [Claude Code](https://claude.ai/code) — no other dependencies needed. All research is done via web search built into Claude Code.

```bash
git clone <this-repo>
cd trip-planner
claude
```

Claude Code loads `CLAUDE.md` automatically when you open the project. The agent is available immediately in the chat.

---

## How to Use

### Step 1 — Open the project

```bash
claude   # opens Claude Code in this directory
```

### Step 2 — Invoke the agent in the chat

Type this in the Claude Code chat (not in your terminal):

```
Use the trip-planner agent for:
- Destinations: Rome, Florence, Venice
- Number of days: 7
- Trip name: italy-2026
- Pace: moderate
- Interests: history, food, art
- Start date: 14 April 2026
```

**Required fields:** Destinations, Number of days, Trip name, Pace
**Optional:** Interests, Start date

**Pace options:**
- `relaxed` — 2–3 main spots per day; long lunches, unhurried
- `moderate` — 3–4 spots per day; structured but not rushed
- `packed` — 5–6 spots per day; early starts, maximum coverage

### Step 3 — Watch it run

| Stage | What it does | Output |
|---|---|---|
| Destination Research | Web searches each city for top spots, hours, coordinates | `destinations.md` |
| Itinerary Building | Schedules spots by day, groups by geography, estimates travel time | `itinerary.md` |
| Maps Enrichment | Adds a Google Maps link to every place | updates `itinerary.md` |
| Trip Formatting | Exports the itinerary to a spreadsheet-ready CSV | `itinerary.csv` |
| Summary | One-page overview with highlights and booking checklist | `trip-summary.md` |

Total time: roughly 5–10 minutes depending on number of cities.

### Step 4 — Find your files

All output lands in `trips/<your-trip-name>/`:

```
trips/italy-2026/
├── trip-summary.md    ← start here: overview, highlights, booking checklist
├── itinerary.md       ← full day-by-day schedule with Maps links
├── itinerary.csv      ← same data, open in Excel or Google Sheets
└── destinations.md    ← all researched spots with hours and coordinates
```

**Read in this order:**
1. `trip-summary.md` — overview and what to book before you go
2. `itinerary.md` — the full schedule (readable in any Markdown viewer)
3. `itinerary.csv` — open in Excel/Sheets for sorting, filtering, sharing

### Step 5 — Open the CSV in Excel or Google Sheets

**Excel:** File → Open → select `itinerary.csv`
**Google Sheets:** File → Import → Upload → select `itinerary.csv`

The CSV has one row per time slot with columns: Day, Date, Time, Place, Category, Famous For, Hours, Visit Duration, Travel to Next, Maps Link, Tips.

---

## Google Maps Links

Each place in the itinerary gets a direct Google Maps link:

- **Coordinate-based** (preferred): opens exactly the right place — `https://www.google.com/maps/place/Colosseum/@41.8902,12.4922,17z`
- **Search fallback**: used for neighborhoods or when coordinates aren't available — `https://www.google.com/maps/search/?api=1&query=Trastevere+Rome+Italy`

---

## Re-running a Single Stage

If you want to refresh just one part:

```
Use the destination-research skill for Tokyo and Kyoto, output to trips/japan-2026.
```

```
Use the itinerary-builder skill on trips/japan-2026, 10 days, moderate pace.
```

The agent and skills always write to the same workspace folder, so you can re-run any stage without losing other files.

---

## Repository Layout

```
trip-planner/
├── CLAUDE.md                         # Auto-loaded project context
├── .claude/
│   └── settings.json                 # Tool permissions
├── agent/
│   └── SKILL.md                      # Orchestrator — runs the full workflow
├── skills/
│   ├── destination-research/
│   │   └── SKILL.md                  # Web search tourist spots, hours, coordinates
│   ├── itinerary-builder/
│   │   └── SKILL.md                  # Schedule spots by day, estimate travel time
│   ├── maps-enricher/
│   │   └── SKILL.md                  # Generate Google Maps links
│   └── trip-formatter/
│       └── SKILL.md                  # Export to CSV
├── trips/                            # Private output workspaces; ignored by Git
│   └── .gitkeep
├── .gitignore
└── README.md
```

---

## Privacy

`trips/` is never committed to Git. All your itinerary files, researched data, and CSV exports stay on your machine.

---

## Planned Future Skills

| Skill | What it will do |
|---|---|
| `hotel-finder` | Recommended hotels per city and budget tier, with booking links |
| `restaurant-finder` | Dining picks grouped by neighborhood and day |
| `budget-estimator` | Rough daily cost breakdown (entry fees, transport, meals) |
| `weather-advisor` | Best travel season and expected weather for your trip dates |

To add a new skill: create `skills/<skill-name>/SKILL.md` and add a stage for it in `agent/SKILL.md`.
