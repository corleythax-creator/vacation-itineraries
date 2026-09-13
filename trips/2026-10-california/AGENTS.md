# AGENTS.md — California Trip & Dashboard

Handoff context for anyone (human or agent) picking up this project. Read §0 before touching the live site.

---

## §0 Read this first

- The dashboard is **live and public**. Anyone with the link can read it. Nothing goes on the page that you would not put on a postcard.
- **Never re-enable Vercel Authentication** on this project. Public access without a Vercel account is a hard requirement.
- **No confirmation numbers on the page.** Not the United record locator, not the Hilton confirmations. A record locator plus a last name is enough for a stranger to modify or cancel the booking.
- There is **no build step**. The dashboard is one `index.html` with inline CSS and JS. Edit it in place and commit; Vercel rebuilds on push.
- Source of truth for trip facts is the user's screenshots and the itinerary PDF — not this file's summaries. Where they conflict, see §5.

---

## §1 The trip

**Thaxton family — Corley, Jessica, Dexter — October 2–8, 2026. Jackson, MS to California and back.**

| | |
|---|---|
| Departs | Oct 2, 5:20 PM from JAN |
| Returns | Oct 8, 9:58 PM to JAN |
| Route | San Francisco → Monterey → Big Sur → Solvang → Anaheim → Hollywood → Santa Monica |
| Driving | ~647 miles |
| Flying | 3,710 miles round trip |
| Park days | Disney California Adventure (Oct 6), Universal Studios Hollywood (Oct 7) |

### Flights — United, all four legs via Houston

| Date | Flight | From | To | Seats |
|---|---|---|---|---|
| Oct 2 | UA 4348 | JAN 5:20 PM | IAH 6:55 PM | 10A / 10C / 10D |
| Oct 2 | UA 1202 | IAH 7:41 PM | SFO 9:50 PM | Row 30 (Jessica 30C) |
| Oct 8 | UA 1084 | LAX 1:08 PM | IAH 6:34 PM | 30C / 30B / 30A |
| Oct 8 | UA 4320 | IAH 8:25 PM | JAN 9:58 PM | 10A / 10C / 10D |

Outbound Houston connection is **46 minutes** — the single tightest link in the trip. Return connection is 1h 51m.

### Lodging

| Nights | Hotel | Status |
|---|---|---|
| Oct 2–4 | Hotel Caza Fisherman's Wharf, 1300 Columbus Ave, SF | Booked |
| Oct 4–5 | Home2 Suites at the Dunes on Monterey Bay | Booked |
| Oct 5–6 | Corque Hotel, Solvang (Tribute Portfolio) | Booked |
| Oct 6–7 | Home2 Suites by Hilton Anaheim Resort | Booked |
| Oct 7–8 | Hilton Santa Monica Hotel & Suites, 1707 4th St | **Not booked** |

Confirmation numbers exist for the Hilton stays, the United reservation and the SIXT rental. They are held by the user and deliberately kept off the site and out of this repo.

### Rental car — SIXT

| | |
|---|---|
| Pickup | Oct 2, 10:30 PM — SFO Rental Car Center, 780 N McDonnell Rd (AirTrain Blue Line) |
| Return | Oct 8, 11:00 AM — 5251 W 98th St, Los Angeles 90045 |
| Vehicle | Midsize SUV, Toyota RAV4 or similar, automatic, 5 seats / 4 bags |
| Renter | Jessica — booking is in her name, so she presents license and credit card |

The **LAX return is off-airport**, not the terminal garage, and requires a shuttle
to the terminal. The Oct 8 morning schedule budgets for it. Load the street address,
not "LAX rental return."

An earlier version of this booking had the pickup at 10:30 **AM** on Oct 2, twelve
hours before the flight lands. The user corrected it to 10:30 PM. If a future
screenshot shows a morning pickup again, that's a regression, not new information.

### Ride heights

Dexter is about 4'7" (55 inches), which clears **every** height minimum at both parks. The
number is deliberately kept here and off the page — the dashboard says only that he clears
every minimum. The
tallest anywhere on the itinerary are 48 inches — Incredicoaster at Disney California
Adventure, Revenge of the Mummy and Forbidden Journey at Universal. Both ride plans state
this rather than gating rides by height; if he is measured shorter at the gate, the plans
still work but the 48-inch rides drop out.

### Fixed times that drive everything else

- **Oct 6, 3:00 PM** — Disney California Adventure entry (three-hour head start before the party)
- **Oct 6, 6:00–11:00 PM** — Oogie Boogie Bash
- **Oct 8, 1:08 PM** — LAX departure; rental car back by 11:00 AM, plus the shuttle

### Deliberately excluded

Alcatraz, Hearst Castle, Beverly Hills, Griffith Observatory, Venice, Malibu. These were cut on purpose to keep days from feeling rushed. Do not "helpfully" add them back.

---

## §2 The dashboard

| | |
|---|---|
| Live URL | https://california-trip-2026-corley.vercel.app |
| Vercel project | `california-trip-2026` |
| Team slug | `corley` |
| Project ID | `prj_isuwwq3jqbyalLOQSHCVGbaublmI` |
| Protection | Vercel Authentication **off**, password **off**, trusted IPs **off** |

### What's on the page

Live countdown → coast route SVG → flights → lodging → rental car → day-by-day (each with a collapsible hour-by-hour, must-do strip, and a "cut this first" note; Oct 6 and Oct 7 also carry collapsible ride plans) → driving and flight mileage tables → logistics notes → summary facts → open items.

That is the source order, and it is what phones and windows under 1080px show. At
1080px and up the lodging block is grid-placed into the space beside the hero, so the
top of the page reads countdown / route map / hotels as one band and Flights starts
below it.

### Architecture

- Single `index.html`, ~48 KB, no framework, no dependencies, no build.
- Fonts from Google Fonts CDN (Bricolage Grotesque for display, Newsreader for body), with system fallbacks.
- One `<script>` block: the countdown, and a `markToday()` that highlights the current day's card and auto-opens its schedule during the trip.
- Hour-by-hour schedules use native `<details>`/`<summary>` — no JS needed to expand.
- **No `localStorage`, `sessionStorage`, or any browser storage.** The page is stateless by design.
- Mobile breakpoint at 760px scales type **up** — these get read on phones, which is where
  this dashboard is opened nearly all the time. Body runs 22px there against 17px on
  desktop, and every small-print class is scaled with it rather than left at its desktop
  size. Checked for overflow at 320 through 760px with every collapsible open.
- **Hero sizing is deliberate.** The countdown column is `minmax(0,max-content)` and the
  route map is capped at 184px so the two sit as one cluster. The `minmax` matters: with
  a plain `max-content` track the countdown never yields, so enlarged type (a wider font
  than the fallback, or text-only zoom) crushes the map instead — at 1.8x it went down to
  78px. Now the clock wraps, which it is built to do, and the map keeps its size; the map's viewBox (`16 10 264 490`)
  is cropped to the drawing's real bounds, and the stop labels are 15px in viewBox units
  so they still render ~11px at that size. Widening the map or restoring a fractional
  grid re-opens the ~250px of dead air this replaced. On mobile the countdown comes
  first and the map follows — don't put `order:-1` back on `.coast`, it pushed the clock
  below the fold on a 390&times;844 phone.
- **The lodging band above 1080px** is CSS only. `.wrap` becomes a two-column grid,
  everything spans both columns, and `.hero` and `.sleep` are placed into row 2 — so the
  hotels move without touching source order, and a screen reader still reads flights
  before hotels. The placement is by row number and assumes the masthead is row 1 and
  the hero row 2; inserting anything above the masthead breaks it. Inside that band the
  map grows to 236px, since the hotel column, not the countdown, sets the band's height.

### Countdown logic

Two fixed UTC instants so the clock reads correctly from any time zone:

```js
var depart = Date.UTC(2026,9,2,22,20);  // Oct 2, 5:20 PM CDT — JAN pushback
var home   = Date.UTC(2026,9,9,2,58);   // Oct 8, 9:58 PM CDT — landing at JAN
```

Three states: counting down to departure → counting down to the flight home while showing "Day N of seven" → trip complete.

---

## §3 How to deploy

The dashboard lives at `trips/2026-10-california/index.html` in the
`vacation-itineraries` repo. Edit and commit — Vercel builds on push to `main`,
with the trip folder set as the project's root directory.

The stable alias `california-trip-2026-corley.vercel.app` repoints automatically.
The per-deploy URL changes each time and is not the one to share.

Deployment protection persists across deploys. Verify with
`get_project_deployment_protection` if the page ever prompts for login.

Verification note: the standard `web_fetch` tool refuses URLs it hasn't seen in a
search result, so anonymous access can't be confirmed from inside a session. Check
in a private browser window.

---

## §4 Working preferences

- Flag conflicts and risks directly rather than smoothing them over. Tight connections, thin morning windows and unbooked nights get called out, not buried.
- Keep prose tight. Tables and cards do the heavy lifting; the reply summarizes what changed and why.
- Round numbers honestly and label estimates as estimates.
- Cost-sensitive about token usage. Don't redeploy twice when one pass will do; don't re-read context that's already in hand.
- Spell out acronyms on first use.

---

## §5 Open items and known conflicts

**Open**
- Oct 7 Santa Monica room — not booked. The screenshot was a search set to **1 guest**; it needs 3.
- Solvang Trolley — October departure times change seasonally and need confirming.
- One-way drop fee on the SIXT rental — confirm it's already in the rate.

**Conflicts between the itinerary PDF and the user's later messages** (page follows the user, not the PDF)
- PDF puts San Francisco at the **Infinity Hotel, Marina District**; the user later said **Hotel Caza, Fisherman's Wharf**. Page uses Caza. Knock-on: the Pier 39 / Ghirardelli / Aquatic Park block is now walkable from the room, and Oct 3 dinner shifts from Chestnut Street to the Wharf or North Beach.
- PDF's Oct 7 morning drives **Santa Monica → Universal**, but Santa Monica is that night's hotel. The real start is **Anaheim → Universal**, 33 miles. Page corrects this and adds the checkout-before-you-leave step, since bags ride along all day.
- PDF names the Hilton Santa Monica as the Oct 7 hotel, implying a booking that doesn't exist yet.

**Checked on Sept 13, 2026** — what was confirmed and what changed on the page as a result.

| Item | Finding | Page |
|---|---|---|
| Oogie Boogie Bash, Oct 6 | Confirmed party night, 6–11 PM, entry from 3:00. Sold out in advance. | Open item added: tickets in the app |
| Bash entertainment | Frightfully Fun Parade is gone. Madame Leota's Swinging Wake street party at **8:05 and 10:15**. Villains Grove, Monsters After Dark, Radiator Screams and Mickey's Trick and Treat (3:30 / 4:30 shows covered by the party ticket) all confirmed. Eleven treat trails. | Oct 6 timeline and ride plan rebuilt around the two showtimes |
| Halloween Horror Nights, Oct 7 | **Oct 7 is an event night.** Day park closes 5:00–6:00; event opens 7:00. October weekdays have been opening at 10:00. | Flag on Oct 7; rope-drop heading no longer says 8:30 |
| Highway 1, Big Sur | **Closed to through traffic** from Kirk Creek Campground (PM 19) to Willow Springs (PM 10.5) for the Plaskett Fire, no reopening estimate. Northern section reopened Sept 10; Nepenthe reopened; Bixby reachable. Pfeiffer Big Sur and Andrew Molera closed until further notice. McWay Falls Overlook Trail closed through 2026 for a retaining wall — roadside view only. | Oct 5 flag carries the out-and-back Plan B: McWay, turn around, Salinas, 101 — about 300 miles |
| Solvang Trolley | Horse-drawn tour runs **weekends and holidays only, 12:00–5:00**. Oct 5 is a Monday, Oct 6 a Tuesday. | Both trolley slots replaced; chip removed |
| SIXT at SFO | Open 24 hours. The 10:30 PM pickup is fine. | — |
| 17-Mile Drive | Sources disagree between $12.25 and $12.50; the $35 restaurant refund is consistent. | Left at $12.50 |
| Time zone note | Was backwards — mornings in California are *easier* on a Central body clock, not harder. The cost is the nights. | Rewritten |
| "Holiday-weekend Friday" on Oct 2 | No holiday that weekend; Columbus Day is Oct 12. | Removed |

### Numbers, maps and deadlines (added Sept 13)

Every phone number on the page was looked up, not recalled, and each is a public main line —
no confirmation numbers, consistent with §0. Hotel Caza's is a 650 number rather than 415;
that is what the hotel publishes, verified twice, so don't "fix" it.

| | Number |
|---|---|
| Hotel Caza | 650-502-4200 |
| Home2 Dunes, Marina | 831-275-4500 |
| Corque, Solvang | 805-688-8000 |
| Home2 Anaheim | 714-215-4855 |
| Hilton Santa Monica | 310-395-3332 |
| SIXT (national, both branches) | 888-749-8227 |
| United | 800-864-8331 |
| Caltrans road conditions | 800-427-7623 |
| Nepenthe | 831-667-2345 |

Map links use `google.com/maps/search/?api=1&query=` with an encoded address, which hands off
to the Maps app on a phone. The Oct 5 Plan B uses the `dir/?api=1` form with Salinas and Paso
Robles as waypoints. `tel:` hrefs are all `+1` E.164 — a plain `tel:650-502-4200` is not
reliably dialable.

Each day with a hard deadline carries a `.deadline` band above its schedule, stating the time
and what breaks if it slips. Oct 3 has none on purpose: it is the one day with no deadline,
and inventing one would dilute the other six.

### Weather strip, packing, speed and fuel (added Sept 13)

The weather strip is the page's **only network call**, and the page is correct without it.
Five cards ship with October normals baked into the HTML; one `fetch` to Open-Meteo
(keyless, CORS-open, free for non-commercial use) replaces them with a real forecast and
relabels each card "Forecast". Any failure — offline, a non-200, the trip still outside the
16-day forecast window — leaves the normals in place and changes nothing else. Tested all
three paths with a mocked response, an aborted request and a 400. No storage, so it re-fetches
on every load. If you ever add another fetch, hold it to the same rule: static content that is
already right, upgraded in place.

Card-to-date mapping is `DAY=[1,3,4,4,6]`, indexes into Oct 2–8: San Francisco on Oct 3,
Monterey on Oct 5, Solvang and Anaheim on Oct 6, Santa Monica on Oct 8.

**Flight speed and fuel** (added Sept 13). Speed is block speed — the scheduled gate-to-gate
times already on the page divided into the distances — which is why it reads 221 to 401 mph
against a cruise speed near 500. Fuel is estimated seat-miles per gallon: about 33 for the
350-mile Jackson hops on a 76-seat regional jet, 70–72 for the long narrowbody legs, 59 across
the trip, or ~63 gallons per seat. The fleet-wide anchor is 67 available seat-miles per gallon
(US airlines, 2024), so the legs sit either side of it, and the short hops are the bad case
because climb dominates a 350-mile sector. Treat all four as estimates; aircraft type is not
confirmed for these specific flights, only inferred from "United Express" and the seat map.

The comparison in the note is the payoff and it is robust to the uncertainty: the rental car
carries three people at 30 mpg, so 90 passenger-miles per gallon — better per person than any
flight leg. The whole drive burns about 22 gallons; one Houston–San Francisco seat burns 23.

**Average mph is moving time, not elapsed** — stops removed. Elapsed-time speeds would read
6 mph for the Oct 4 sightseeing loop, which is true and useless. The figures are planning
estimates, not measurements: 17 hours behind the wheel over five driving days, 38 mph overall.

**Fuel** is $5.93/gal (California regular, AAA, Sept 11 2026) over ~30 mpg for a non-hybrid
midsize SUV: 20¢/mile, ~$130 for 647 miles. The 2026 RAV4 is hybrid-only at 41 mpg combined,
which is 14¢/mile and ~$95 — the page gives both because "RAV4 or similar" could be either.
Re-price before departure; California moves fast.

**Still to recheck before departure**
Highway 1 south of Big Sur (Caltrans QuickMap the night before and the morning of), Universal's posted hours for Oct 7, and that all three Bash tickets are in the Disneyland app. Everything else above was verified against sources dated September 2026.

The **Oct 7 ride plan** assumes the 8:30 rope drop the timeline protects. Confirm Universal's
2026 park hours, whether Oct 7 is a Halloween Horror Nights night (the day park closes early
on event nights, often 5:00 PM, and Lower Lot attractions can shut mid-afternoon for setup),
whether Super Nintendo World is on a virtual line, and which rides run single rider. The
structure — Upper Lot headliners at rope drop, one trip down the StarWay, the Studio Tour
before the midday peak — survives any of those answers.

The **Oct 6 ride plan** is written from how the Bash has run in recent seasons, not from a
published 2026 lineup. Everything in it with a time or a number attached needs confirming:
the roughly 6:30 and 9:00 parade showings, whether Guardians runs its Monsters After Dark
overlay, which rides stay open during the party, and every height minimum. The strategy
underneath it — spend the 3:00-6:00 head start on Radiator Springs Racers and Incredicoaster,
leave the low-wait rides and the party-exclusive overlay for after 6:00 — holds regardless of
what the lineup turns out to be.
