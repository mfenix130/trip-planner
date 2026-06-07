# Flight Research Skill

Search for flight options and price ranges between the user's home airports and their destination cities. Produces a structured `flights.md` with routes, price ranges, airline options, and direct search links to aggregator sites.

---

## Runtime Rules

- Default origin airports: **SFO** (San Francisco) and **SJC** (San Jose). These are overridden by the user's `from` input.
- Only report what searches return. Do not fabricate prices, schedules, or availability.
- Mark all prices as estimates — flight prices are dynamic and change constantly.
- Prioritise routes with the fewest stops. If nonstop exists, always mention it.
- This skill cannot book flights. It surfaces information and links — the user books themselves.

---

## Purpose

Help the user understand flight options and rough costs before they finalise trip dates. For multi-city trips, also suggest the most efficient routing (e.g. fly into Rome, out of Venice vs. flying in and out of the same city).

---

## Inputs

From the user or agent:
- **Destinations** — list of cities in trip order *(required)*
- **Dates** — departure date and return date, or a date range *(required for this skill — skip if not provided)*
- **From** — origin airport code(s) or city name *(optional; defaults to SFO and SJC)*
- **Cabin class** — economy / premium economy / business *(optional; defaults to economy)*
- **Number of travellers** — *(optional; defaults to 1)*

**If no dates are provided:** skip this skill entirely and note in the agent summary that flight research requires dates. Do not attempt to search without dates — results will be meaningless.

---

## Route Logic

### Single-City Trip
- Search: `[origin] → [destination city airport]` outbound
- Search: `[destination city airport] → [origin]` return
- Report both legs separately

### Multi-City Trip
Two options to evaluate and compare:

**Option A — Hub routing:** Fly into and out of the same city (usually the first destination)
- `[origin] → [City 1]` outbound
- `[City N] → [origin]` return (if different from City 1, note the one-way open-jaw)

**Option B — Open-jaw routing:** Fly into first city, out of last city
- `[origin] → [City 1]` outbound
- `[City N] → [origin]` return
- This avoids backtracking at the end of the trip — note whether this saves meaningful travel time

Recommend whichever option is more efficient. If prices differ significantly, note both.

---

## Search Steps

### Step 1: Identify Airports

For each destination city, identify the main airport(s):
- Search: `"[city name] airport code"`
- For cities with multiple airports (e.g. London: LHR/LGW/STN), list the main one and note alternatives
- Record: city → airport code(s)

### Step 2: Search Flight Prices

For each route leg, search aggregator sites for price ranges and airline options:

**Primary search queries:**
- `"flights from SFO to [destination airport] [month year]"`
- `"flights from SJC to [destination airport] [month year]"`
- `"[origin] to [destination] flight price [dates]"`

**Aggregator sites to check (in order):**
1. **Google Flights** — `https://www.google.com/travel/flights` — best for price overview and calendar view
2. **Kayak** — `https://www.kayak.com/flights` — good for flexible date searches
3. **Skyscanner** — `https://www.skyscanner.com` — strong for international and multi-city

For each route, extract:
- Price range (low / typical / high) in USD
- Nonstop availability (yes/no)
- If no nonstop: common connection cities and layover duration
- Airlines commonly operating the route
- Approximate flight duration (nonstop if available, otherwise with connection)

### Step 3: Build Direct Search Links

Construct deep links into Google Flights for each route so the user can see live prices with one click.

**Google Flights URL format:**

```
https://www.google.com/travel/flights/search?tfs=CBwQAhoeEgoyMDI2LTA0LTE0agcIARIDU0ZPcgcIARIDFkNEgA
```

Since Google Flights URLs are complex and session-based, use the simpler search URL instead:

```
https://www.google.com/travel/flights?q=Flights+from+SFO+to+Rome+on+April+14+2026
```

This opens Google Flights with the query pre-filled. Construct one link per route leg.

**Kayak direct search URL format:**
```
https://www.kayak.com/flights/SFO-FCO/2026-04-14/2026-04-21
```
Format: `/flights/[ORIGIN]-[DESTINATION]/[OUTBOUND-DATE]/[RETURN-DATE]`
For one-way: `/flights/[ORIGIN]-[DESTINATION]/[DATE]`

Use IATA airport codes. This is the most reliable deep-link format — always include a Kayak link.

**Skyscanner URL format:**
```
https://www.skyscanner.com/transport/flights/sfo/fco/260414/260421/
```
Format: `/transport/flights/[origin-lower]/[dest-lower]/[YYMMDD-outbound]/[YYMMDD-return]/`

### Step 4: Check for Nearby Airport Savings

For origin airports — check if flying from the other Bay Area airports (OAK, SFO, SJC) produces significantly different prices:
- Search: `"cheapest flights to [destination] from Bay Area"`
- If OAK is meaningfully cheaper (>$100/person), flag it

---

## Output

Write one file: `flights.md`

```markdown
# Flight Research

**Trip:** [trip name]
**Destinations:** [cities]
**Travel dates:** [outbound date] → [return date]
**Origin airports:** [SFO, SJC] (or user-specified)
**Cabin:** Economy
**Travellers:** 1
**Researched:** [date] — prices are estimates and change daily

---

## Recommended Routing

[1–2 sentences: recommend open-jaw vs. hub routing and why]

---

## Route: SFO → [Destination Airport] ([City])

**Date:** [outbound date]
**Airport:** [Destination airport name] ([code])
**Nonstop:** [Yes / No — if no, note common connections]
**Duration:** [X hr Y min nonstop / X hr Y min via [city]]
**Airlines:** [list]
**Price range:** ~$[low]–$[high] economy per person *(estimate — check aggregators)*

| Book on | Link |
|---------|------|
| Google Flights | [Search SFO → FCO on Apr 14](https://www.google.com/travel/flights?q=Flights+from+SFO+to+Rome+April+14+2026) |
| Kayak | [SFO → FCO Apr 14](https://www.kayak.com/flights/SFO-FCO/2026-04-14) |
| Skyscanner | [SFO → FCO](https://www.skyscanner.com/transport/flights/sfo/fco/260414/) |

---

## Route: [Return Airport] → SFO

**Date:** [return date]
**Nonstop:** [Yes / No]
**Duration:** [X hr Y min]
**Airlines:** [list]
**Price range:** ~$[low]–$[high] economy per person *(estimate)*

| Book on | Link |
|---------|------|
| Google Flights | [...](URL) |
| Kayak | [...](URL) |
| Skyscanner | [...](URL) |

---

## SJC Alternative

[If SJC prices differ meaningfully from SFO, summarise the comparison here. Otherwise: "SJC routes to [destination] are limited — SFO is the better origin for this trip."]

---

## OAK Savings Check

[If OAK is significantly cheaper: flag it. Otherwise: omit this section.]

---

## Total Estimated Round-Trip Cost

| Origin | Route | Price Range (economy, per person) |
|--------|-------|----------------------------------|
| SFO | SFO → [dest] → SFO | ~$[low]–$[high] |
| SJC | SJC → [dest] → SFO (open jaw) | ~$[low]–$[high] |

*Prices are point-in-time estimates. Book directly on the aggregator links above for current fares.*

---

## Tips

- [e.g. "Nonstop from SFO on United is typically the fastest option but commands a premium; one-stop via Frankfurt on Lufthansa is often $200–300 cheaper"]
- [e.g. "Prices drop significantly if you fly Tuesday/Wednesday vs. Friday/Sunday"]
- [e.g. "For this route, booking 6–8 weeks ahead typically gives the best economy fares"]
- [e.g. "Consider a city pass or airport-to-city transit card on arrival — adds $15–25 but saves time"]

---

## Research Notes

[Any routes that returned no results, searches that failed, caveats on data quality]
```

---

## Tone

Factual and practical. Give the user enough to make a booking decision, not a travel blog essay. Flag uncertainty clearly — flight prices are volatile and this is research, not a quote.
