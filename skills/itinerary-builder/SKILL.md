# Itinerary Builder Skill

Schedule tourist spots across days in a logical, time-respecting order — grouped by geography, paced appropriately, and annotated with travel time between each stop.

---

## Runtime Rules

- Always read `destinations.md` before building the itinerary — never schedule from memory.
- Respect opening hours. Do not schedule a place before it opens or after it closes.
- Group spots geographically within each day to minimise unnecessary transit.
- Build a realistic schedule — leave buffer for meals, rest, spontaneity.
- Use Option C for travel time: heuristics for nearby spots, web search for complex legs.
- Do not over-pack. A great day has 3–4 highlights. A mediocre day has 7 rushed ones.

---

## Purpose

Turn the candidate spots from `destinations.md` into a concrete, time-blocked, day-by-day itinerary. The output should be ready to hand to a traveller with no extra thinking required — they just follow it.

---

## Inputs

Required:
- `destinations.md` — candidate spots with hours, coordinates, visit durations
- **Number of days** — provided by user
- **Pace** — `relaxed`, `moderate`, or `packed`

Optional:
- **Start date** — if provided, include actual dates (Mon 14 Apr); otherwise use Day 1, Day 2, etc.
- **Interests** — already filtered in destinations.md, but use for emphasis when choosing between candidates

---

## Pace Definitions

| Pace | Spots per day | Daily rhythm |
|------|--------------|-------------|
| `relaxed` | 2–3 main spots + free exploration | Long lunches, unhurried, museum fatigue avoided |
| `moderate` | 3–4 main spots | Structured but not rushed; 1–2 optional add-ons |
| `packed` | 5–6 spots | Early starts, efficient transit, limited lingering |

For multi-city trips: schedule inter-city travel on its own half-day or full day depending on journey length.

---

## Scheduling Rules

### Geographic Grouping
- On any given day, keep spots within the same neighborhood or district when possible
- Never schedule spots on opposite sides of a city back-to-back without accounting for 30–45 min transit
- "Anchor" each day around the highest-priority spot, then fill nearby spots around it

### Time of Day Optimisation
| Time slot | Best for |
|-----------|---------|
| Early morning (8–10am) | Popular monuments before crowds (Colosseum, Eiffel Tower), outdoor markets |
| Late morning (10am–12pm) | Museums, galleries, historical sites |
| Early afternoon (12–2pm) | Lunch in a local neighborhood; use as a transit break |
| Afternoon (2–5pm) | Neighborhoods, markets, secondary sights, walking |
| Late afternoon / Golden hour | Viewpoints, rooftop bars, scenic walks |
| Evening | Dinner district, night markets, shows, lit landmarks |

### Booking Flags
- If a spot in `destinations.md` is marked "Book Ahead: Yes", add a `⚠ Book in advance` note in the Tips column
- List all booking-required spots in the trip-summary for easy reference

### Inter-City Travel Days
- If multiple cities are included and the journey is <2 hours: schedule morning travel, start the new city in the afternoon
- If journey is 2–4 hours: treat as a half travel day; 1–2 light spots at the destination only
- If journey is >4 hours or requires a flight: dedicate a full travel day; no major sightseeing

---

## Travel Time Estimation (Option C)

### Heuristics (apply first)

Use for spots in the same city/neighborhood:

| Scenario | Estimate |
|---------|---------|
| Walking ≤ 500m | 5–8 min walk |
| Walking 500m–1.5km | 10–20 min walk |
| Walking 1.5–3km | 25–35 min walk (or 10 min taxi/metro) |
| Walking > 3km | 15–25 min metro/bus, or 15–20 min taxi |
| Within same tourist district | 10–15 min |
| Cross-city within same urban area | 20–35 min (metro) or 20–30 min (taxi) |

**Rule of thumb:** When in doubt, add 10 minutes for finding the entrance, paying, and settling in.

### Web Search (use for)

- Inter-city travel legs (always web search — heuristics do not apply)
- Any leg where the mode of transport is unclear
- Known congestion cities (Bangkok, Cairo, Mumbai) where car travel is unpredictable
- Ferry, cable car, or unusual transit options

Search: `"travel time from [Place A] to [Place B] in [City]"`

Report the web-searched estimate with a note: `(web search estimate)`

---

## Output Format

Write one file: `itinerary.md`

The file has two sections:
1. A Markdown table — the machine-readable, maps-enricher-ready schedule
2. A narrative day summary — one paragraph per day for human context

```markdown
# Trip Itinerary: [Trip Name]

**Destinations:** [cities]
**Duration:** [N] days
**Pace:** [relaxed / moderate / packed]
**Generated:** [date]

---

## Day-by-Day Schedule

<!-- Maps links will be added by the maps-enricher skill -->

| Day | Date | Time | Place | Category | Famous For | Hours | Visit Duration | Travel to Next | Maps Link | Tips |
|-----|------|------|-------|----------|------------|-------|----------------|----------------|-----------|------|
| Day 1 | Mon 14 Apr | 9:00 AM | Colosseum | Monument | Largest Roman amphitheatre; held 50,000 spectators | 9am–7pm | 2–3 hrs | 15 min walk → | — | ⚠ Book in advance; get there at opening |
| Day 1 | Mon 14 Apr | 12:00 PM | Roman Forum | Archaeological Site | Ancient centre of Roman public life; walks through 2,000 years of ruins | 9am–7pm | 1–2 hrs | 20 min walk → | — | Combo ticket with Colosseum |
| Day 1 | Mon 14 Apr | 2:00 PM | Lunch in Testaccio | Neighborhood | Rome's most authentic food market neighborhood | All day | 1.5 hrs | 10 min taxi → | — | Try supplì and cacio e pepe |
| Day 1 | Mon 14 Apr | 4:00 PM | Palatine Hill | Monument | Birthplace of Rome; ruins of imperial palaces with views over the city | 9am–7pm | 1.5 hrs | 25 min walk → | — | Combo ticket with Colosseum |
| Day 1 | Mon 14 Apr | 7:30 PM | Trastevere | Neighborhood | Rome's most charming medieval neighborhood; evening dining and nightlife | All day | 2–3 hrs | — | — | Best after sunset; wander without a plan |
| Day 2 | ... | | | | | | | | | |

---

## Day Summaries

### Day 1 — Ancient Rome + Trastevere
[2–4 sentence narrative: what the day covers, the character of the experience, any practical tips for the day as a whole]

### Day 2 — ...
[...]

---

## Booking Required

Places that need advance booking — action these before the trip:

| Place | Why | How to Book |
|-------|-----|------------|
| Colosseum | Sells out weeks ahead, especially in summer | trenitalia.it or official site |
| ... | | |

---

## Inter-City Travel

| Leg | From | To | Method | Duration | Approx Cost | Notes |
|-----|------|----|--------|----------|-------------|-------|
| Day 3 → Day 4 | Rome | Florence | Frecciarossa (high-speed train) | 1h 30min | €30–80 | Book 2–3 weeks ahead; departs from Roma Termini |
```

---

## Tone

Practical and direct. Write itineraries that a first-time traveller could follow without getting lost. Day summaries should be warm but concise — not a travel blog. Tips should be actionable, not obvious.
