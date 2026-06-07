# Destination Research Skill

Research tourist spots, attractions, and practical information for each destination city. Produces a structured file used by itinerary-builder and maps-enricher.

---

## Runtime Rules

- Always start fresh on rerun — do not reuse prior session state.
- Only report what you can find. Do not invent hours, prices, or coordinates.
- Mark uncertain data as `[unverified]`.
- Prioritize current information (last 12 months) for hours and prices — these change.
- Aim for 8–15 candidate spots per city. The itinerary-builder will select from these; more candidates = better scheduling.

---

## Purpose

Produce a complete, well-structured research file with enough data for the itinerary-builder to schedule intelligently — including coordinates (for Maps links), opening hours (to avoid scheduling conflicts), visit durations, and what makes each place worth visiting.

---

## Inputs

From the user or agent:
- **Cities / areas** — one or more destination names
- **Interests** (optional) — history, food, art, nature, nightlife, shopping, architecture, etc.
- **Number of days** (optional, for scoping how many spots to find)

---

## Steps

### For Each Destination City

#### Step 1: Find Top Attractions

Search for:
- "top tourist attractions in [city]"
- "best things to do in [city]"
- "must-see places in [city]"
- If interests provided: "best [interest] in [city]" (e.g. "best street food markets in Bangkok")

Collect 8–15 candidate spots across categories:
- **Monuments & landmarks** (iconic, historically significant)
- **Museums & galleries** (art, history, science)
- **Neighborhoods & markets** (walkable areas, local life)
- **Parks & nature** (green spaces, viewpoints, outdoor)
- **Religious sites** (temples, churches, mosques — culturally significant)
- **Experiences** (cooking classes, sunset viewpoints, boat rides, shows)

Prioritize variety — at least 2–3 categories per city. If interests are specified, weight toward those but include at least 2 general landmarks.

#### Step 2: For Each Spot, Research

For each candidate spot, find:

**Identity:**
- Full official name
- Neighborhood / district it's in
- Category (Monument, Museum, Neighborhood, Park, Religious Site, Experience, etc.)

**What it's famous for:**
- 1–2 sentence callout — what makes this place iconic or worth visiting
- Any notable facts (oldest, tallest, most visited, etc.)

**Practical info:**
- Opening hours (include seasonal variations if relevant — e.g. "Apr–Sep: 9am–7pm; Oct–Mar: 9am–5pm")
- Closed days (e.g. "Closed Mondays")
- Entry fee (free / price per adult in local currency and USD equivalent)
- Booking required? (yes/no — flag places that sell out or need advance booking)

**Visit logistics:**
- Recommended visit duration (e.g. "1–2 hours", "half day")
- Best time to visit (morning to avoid crowds, sunset for views, etc.)
- Nearest transit stop or parking if relevant

**Location:**
- Full address
- Coordinates (latitude, longitude) — search "[place name] coordinates" or "[place name] GPS"
  - Format: decimal degrees, e.g. 41.8902, 12.4922
  - If coordinates cannot be confirmed, mark as `[unverified]` and record the address only

#### Step 3: Research Inter-City Travel (if multiple cities)

If the trip includes multiple cities, find:
- Best travel method between cities (train, flight, bus, ferry)
- Journey time and approximate cost
- Frequency (hourly trains vs. daily flights)
- Booking notes (e.g. "book Eurostar 2–3 weeks ahead for best prices")

Search: "best way to travel from [city A] to [city B]", "train [city A] to [city B] duration"

---

## Output

Write one file: `destinations.md`

```markdown
# Destination Research

## Trip Overview
- Cities: [list]
- Research date: [today's date]
- Interests filter: [if provided, else "none"]

---

## [City Name]

### Candidate Spots

| # | Name | Neighborhood | Category | Famous For | Hours | Closed | Entry | Book Ahead | Visit Duration | Best Time | Address | Coordinates |
|---|------|-------------|----------|------------|-------|--------|-------|------------|----------------|-----------|---------|-------------|
| 1 | Colosseum | Celio | Monument | Largest amphitheatre of the Roman Empire; held gladiatorial contests for 50,000 spectators | 9am–7pm (Apr–Sep) | Never | €18 | Yes — sells out weeks ahead | 2–3 hrs | Morning (9am, before crowds) | Piazza del Colosseo, 1, Rome | 41.8902, 12.4922 |
| 2 | ... | | | | | | | | | | | |

### Practical City Notes
- **Getting around:** [transit options, walking feasibility, taxi norms]
- **Neighborhoods to know:** [brief map of key areas]
- **Watch out for:** [pickpockets, tourist traps, scams common in this city]
- **Tipping customs:** [local norm]

---

## Inter-City Travel

| From | To | Best Method | Duration | Approx Cost | Notes |
|------|----|------------|----------|-------------|-------|
| Rome | Florence | High-speed train (Frecciarossa) | 1h 30min | €30–80 | Book via Trenitalia; runs every 30 min |
| Florence | Venice | High-speed train | 2h 10min | €30–70 | Book ahead; direct trains available |

---

## Research Notes
[Any sources that were unavailable, data marked unverified, or caveats on the research]
```

---

## Tone

Factual and efficient. This file is consumed by other skills — write for machine readability (clean tables) as much as human readability. Flag anything uncertain. Do not pad with generic travel advice.
