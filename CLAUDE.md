# trip-planner

Expert trip planner agent and skills. Takes destination cities, number of days, and preferences — produces a day-by-day itinerary with Google Maps links, opening hours, travel times, and a CSV file for Excel/Sheets.

## Invoking the Agent

Run the full workflow end-to-end:

```
Use the trip-planner agent for:
- Destinations: Rome, Florence, Venice
- Number of days: 7
- Trip name: italy-2026
- Pace: moderate
- Interests: history, food, art
- Start date: 14 April 2026
- Travel dates: 14 April – 21 April 2026
- From: SFO
```

**Pace options:** `relaxed` (2–3 spots/day) · `moderate` (3–4 spots/day) · `packed` (5–6 spots/day)
**Interests:** optional filter — e.g. history, food, art, nature, nightlife, shopping, architecture
**From:** origin airport or city — defaults to **SFO and SJC** if not specified
**Travel dates:** required for flight research (Stage 0); itinerary runs without them

The agent reads `agent/AGENT.md` and runs all stages in sequence, writing output to `trips/<name>/`.

## Skills (run individually if needed)

| Skill | Invoke with | Output |
|---|---|---|
| `flight-research` | "Use the flight-research skill for trips/[name], flying [dates] from [origin]" | `flights.md` |
| `destination-research` | "Use the destination-research skill for [cities], output to trips/[name]" | `destinations.md` |
| `itinerary-builder` | "Use the itinerary-builder skill on trips/[name], [N] days, [pace]" | `itinerary.md` |
| `maps-enricher` | "Use the maps-enricher skill on trips/[name]" | updates `itinerary.md` |
| `trip-formatter` | "Use the trip-formatter skill on trips/[name]" | `itinerary.csv` |

Each skill's full instructions are in `skills/<name>/SKILL.md`. Read the skill file before executing — do not improvise from memory.

## Workspace Layout

All output goes in `trips/<name>/` (gitignored).

```
trips/<name>/
├── destinations.md    ← destination-research output (all spots, coordinates, hours)
├── itinerary.md       ← day-by-day schedule as MD table with Maps links
├── itinerary.csv      ← same data, Excel/Sheets-importable
└── trip-summary.md    ← agent final report
```

## Google Maps Links

Coordinate-based (preferred): `https://www.google.com/maps/place/NAME/@LAT,LNG,17z`
Search fallback: `https://www.google.com/maps/search/?api=1&query=PLACE+NAME+CITY`

Always use coordinate-based when lat/lng is available. Fall back to search URL only when coordinates were not found.

## Travel Time Estimation

Option C: heuristics first, web search for complex legs.

**Heuristics (within same city/neighborhood):**
- Walking ≤0.5 km → 5–8 min
- Walking 0.5–1.5 km → 10–20 min
- Walking 1.5–3 km → 25–35 min
- Metro/bus within city → 15–25 min
- Taxi/rideshare within city → 10–20 min

**Web search for:**
- Inter-city legs (e.g. Rome → Florence by train)
- Long cross-city transfers
- Cases where transit mode is unclear

## Future Skills (not yet built)

- `hotel-finder` — recommended hotels per city by budget tier, with booking links
- `restaurant-finder` — dining picks per neighborhood/day
- `budget-estimator` — rough cost breakdown per day
- `weather-advisor` — best season info and expected weather during trip dates
