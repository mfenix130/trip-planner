# Maps Enricher Skill

Generate Google Maps links for every place in the itinerary. Prefers coordinate-based URLs (which open exactly the right place); falls back to search URLs when coordinates are unavailable.

---

## Runtime Rules

- Read `destinations.md` first to get coordinates already found during research.
- Do not fabricate coordinates. If a coordinate cannot be confirmed, use the search URL fallback.
- For each place, try the coordinate URL format first — only fall back to search if coordinates are missing or marked `[unverified]`.
- This skill modifies `itinerary.md` in place — it reads the file, adds Maps links, and rewrites it.

---

## Purpose

Attach a working, precise Google Maps link to every place in the itinerary so users can tap directly into navigation, check real-time hours, and save places to a Maps list.

---

## Inputs

Required:
- `itinerary.md` — output from itinerary-builder (contains place names per day)
- `destinations.md` — contains coordinates and addresses from destination-research

---

## URL Construction

### Primary: Coordinate-based URL

Use when lat/lng coordinates are available and not marked `[unverified]`:

```
https://www.google.com/maps/place/PLACE+NAME/@LAT,LNG,17z
```

**Rules:**
- Replace spaces in place name with `+`
- Use decimal degree coordinates (e.g. 41.8902, 12.4922)
- Zoom level `17z` works well for most attractions — use `15z` for large parks or neighborhoods
- Example: `https://www.google.com/maps/place/Colosseum/@41.8902,12.4922,17z`

### Fallback: Search URL

Use when coordinates are missing, `[unverified]`, or the place is a neighborhood/large area:

```
https://www.google.com/maps/search/?api=1&query=PLACE+NAME+CITY
```

**Rules:**
- Include the city name in the query for disambiguation
- Replace spaces with `+`
- Example: `https://www.google.com/maps/search/?api=1&query=Trastevere+Rome+Italy`

### When to Use Fallback

- Coordinates not found during destination-research
- Coordinates marked `[unverified]`
- Place is a neighborhood, market area, or walking zone (not a single point)
- Place name is generic (e.g. "Local food market") and coordinates would be imprecise

---

## Steps

### Step 1: Build a Place → URL Map

For every place listed in `itinerary.md`:
1. Look it up in `destinations.md` by name
2. Check if coordinates are present and not marked `[unverified]`
3. Construct the appropriate URL (coordinate-based or search fallback)
4. Record: `Place Name → URL`

For inter-city travel legs (e.g. "Train: Rome → Florence"), generate a Maps directions link:
```
https://www.google.com/maps/dir/ORIGIN/DESTINATION
```
Example: `https://www.google.com/maps/dir/Rome+Italy/Florence+Italy`

### Step 2: Update itinerary.md

Read `itinerary.md`. For every row in the table that has a place name:
- Find the corresponding Maps URL from Step 1
- Replace the placeholder `—` (or empty Maps Link cell) with a Markdown link:
  `[Maps](URL)`

Do not change any other content. Only touch the Maps Link column.

Rewrite the file with all links inserted.

---

## Output

Rewrites `itinerary.md` in place — no new file created.

Every row in the itinerary table should have a populated Maps Link column after this skill runs.

Note in a comment at the top of the Maps Link section of the file how many links used coordinate-based URLs vs. search fallback, e.g.:
`<!-- Maps links: 14 coordinate-based, 3 search fallback -->`
