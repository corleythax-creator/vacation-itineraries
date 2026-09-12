# vacation-itineraries

Trip dashboards and planning context for family vacations. One folder per trip, one
self-contained `index.html` per trip, deployed to Vercel.

## Structure

```
trips/
  YYYY-MM-<place>/
    index.html     the dashboard — single file, no build, no dependencies
    AGENTS.md      handoff context: the trip facts, the deploy setup, open items
    assets/        optional — screenshots, PDFs, confirmations (gitignored if sensitive)
```

Folders sort chronologically because they lead with the year and month. A trip
folder is never renamed after it ships, even once the trip is over — old trips stay
as reference for the next one.

## Conventions

**Single file.** Each dashboard is one `index.html` with inline CSS and JS. No
framework, no build step, no npm. It should open correctly by double-clicking it
from disk.

**No confirmation numbers.** These pages get shared as public links. A record
locator plus a last name is enough for a stranger to modify or cancel a booking.
Confirmations stay in email, never in the repo and never on the page.

**No browser storage.** No `localStorage`, no `sessionStorage`. The pages are
stateless so they behave the same on every device the family opens them on.

**Countdown uses fixed UTC instants**, not local time strings, so the clock reads
correctly regardless of which time zone the viewer is in.

**Mobile first.** These get read on phones, standing in a parking lot. The mobile
breakpoint at 760px scales type up, not down.

## Starting a new trip

Copy the most recent trip folder, then work through it in this order:

1. Replace the trip facts — flights, lodging, day-by-day, mileage tables.
2. Update the two UTC timestamps in the countdown script (departure and return).
3. Update the route SVG stop labels, or drop the SVG if the trip isn't a road trip.
4. Rewrite `AGENTS.md` for the new trip. Delete the old open items rather than
   carrying them forward.
5. Create a Vercel project pointed at the new trip's folder as its root directory.

The California 2026 folder is the reference implementation. It has the fullest
set of patterns: collapsible hour-by-hour schedules, must-do strips, "cut this
first" notes, mileage tables, and an open-items box.

## Deploying

Each trip is its own Vercel project with the trip folder set as the root
directory. Pushing to `main` redeploys whichever trips changed.

Framework preset is **Other**, with no build command and no install command —
there is nothing to build. Deployment protection must be off, since these are
meant to be opened by family members who don't have Vercel accounts.

Note on migrating an existing project to git: connecting the repo does not
retroactively build anything, and redeploying an older CLI-uploaded deployment
will fail once Root Directory points at a trip folder that upload never
contained. Push a commit to trigger the first git build.

## Trips

| Trip | Dates | Status |
|---|---|---|
| [2026-10-california](trips/2026-10-california) | Oct 2–8, 2026 | Upcoming |
