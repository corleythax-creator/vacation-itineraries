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

Live countdown → coast route SVG → flights → lodging → rental car → day-by-day (each with a collapsible hour-by-hour, must-do strip, and a "cut this first" note) → driving and flight mileage tables → logistics notes → summary facts → open items.

### Architecture

- Single `index.html`, ~48 KB, no framework, no dependencies, no build.
- Fonts from Google Fonts CDN (Bricolage Grotesque for display, Newsreader for body), with system fallbacks.
- One `<script>` block: the countdown, and a `markToday()` that highlights the current day's card and auto-opens its schedule during the trip.
- Hour-by-hour schedules use native `<details>`/`<summary>` — no JS needed to expand.
- **No `localStorage`, `sessionStorage`, or any browser storage.** The page is stateless by design.
- Mobile breakpoint at 760px scales type **up** — these get read on phones.
- **Hero sizing is deliberate.** The countdown column is `max-content` and the route map
  is capped at 184px so the two sit as one cluster; the map's viewBox (`16 10 264 490`)
  is cropped to the drawing's real bounds, and the stop labels are 15px in viewBox units
  so they still render ~11px at that size. Widening the map or restoring a fractional
  grid re-opens the ~250px of dead air this replaced. On mobile the countdown comes
  first and the map follows — don't put `order:-1` back on `.coast`, it pushed the clock
  below the fold on a 390&times;844 phone.

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

**Recheck close to the trip**
Highway 1 conditions through Big Sur (Caltrans), Universal park hours, Disney's final Bash entertainment lineup, the Solvang Trolley schedule.
