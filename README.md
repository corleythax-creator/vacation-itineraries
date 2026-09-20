# vacation-itineraries

Trip dashboards and planning context for family vacations. One folder per trip, one
self-contained `index.html` per trip, deployed to Vercel.

## Structure

```
trips/
  YYYY-MM-<place>/
    index.html            the dashboard — one file, no build, no dependencies
    manifest.webmanifest  name, colours and icons for Add to Home Screen
    icon-32.png           favicon
    icon-180.png          iOS home screen
    icon-192.jpg          Android home screen
    icon-512.jpg          Android splash and store-style listings
    AGENTS.md             handoff context: the trip facts, the deploy setup, open items
    assets/               optional — screenshots, PDFs, confirmations (gitignored if sensitive)
```

Folders sort chronologically because they lead with the year and month. A trip
folder is never renamed after it ships, even once the trip is over — old trips stay
as reference for the next one.

## Conventions

**Single file.** Each dashboard is one `index.html` with inline CSS and JS. No
framework, no build step, no npm. It should open correctly by double-clicking it
from disk. The icon files and the manifest are the sole exception — iOS will not take a
home-screen icon from a data URI, so those have to be real files. They are still static;
nothing is compiled.

**Add to Home Screen.** Each trip ships an icon set and a button that offers to install the
page. Chrome gets the native prompt through `beforeinstallprompt`; Safari has no such API, so
there the button opens the Share-sheet instructions instead. The button hides itself once the
page is running from the home screen. Keep `display` at `minimal-ui` and leave
`apple-mobile-web-app-capable` unset: these pages are mostly outbound links to maps and phone
numbers, and a full standalone window strands you with no way back.

**No confirmation numbers.** These pages get shared as public links. A record
locator plus a last name is enough for a stranger to modify or cancel a booking.
Confirmations stay in email, never in the repo and never on the page.

**Network calls degrade, or don't ship.** A dashboard may fetch live data — California 2026
pulls a forecast from Open-Meteo, keyless — but only where the page is already correct
without it. Bake the static answer into the HTML, upgrade it in place on success, and leave
it alone on any failure. No API keys in a public file, and test the offline path before you
ship it: these pages get opened in parking lots.

**No browser storage.** No `localStorage`, no `sessionStorage`. The pages are
stateless so they behave the same on every device the family opens them on.

**Countdown uses fixed UTC instants**, not local time strings, so the clock reads
correctly regardless of which time zone the viewer is in.

**Content-sized layout.** Columns are sized to what's in them, not to fractions of the
page. A `1.25fr .75fr` split leaves the short column floating in dead air and a
decorative graphic drifting inside a track wider than itself. Size the text column to
`max-content`, cap the graphic so its height lands near the text block's, and crop any
SVG's `viewBox` to the drawing's real bounds.

**Fill wide screens with content, not air.** Where a hero leaves a void on a wide
screen, a short section can be grid-placed into it rather than sitting in a band of its
own — California puts the hotels beside the countdown above 1080px. Do it with grid
placement, never by moving the markup: source order stays the reading order for phones,
narrow windows and screen readers.

**Give a long page a spine.** Past roughly fifteen phone screens, a dashboard needs a way to
move around it — California uses a sticky row of section chips below 760px, with ids on every
heading and `scroll-margin-top` so headings clear the bar. Sticky chrome must be opaque;
translucent bars let body text read through them.

**Mobile first.** These get read on phones, standing in a parking lot. The mobile
breakpoint at 760px scales type up, not down, and it covers the small print too — labels,
chips, statuses, table headers, footnotes. Those are the sizes that fail in a parking lot,
and they are the easiest ones to leave behind when only body text gets bumped. The countdown comes before decorative
artwork — a tall graphic stacked above it pushes the clock off the first screen.

## Starting a new trip

Copy the most recent trip folder, then work through it in this order:

1. Replace the trip facts — flights, lodging, day-by-day, mileage tables.
2. Update the two UTC timestamps in the countdown script (departure and return).
3. Update the route SVG stop labels, or drop the SVG if the trip isn't a road trip. If
   you move the stops, re-crop the `viewBox` to the drawing's bounds and keep the label
   `font-size` in viewBox units large enough to land near 11px on screen — at the 184px
   map width that's 15 units, not 11.
4. Rewrite `AGENTS.md` for the new trip. Delete the old open items rather than
   carrying them forward.
5. Open it at a desktop width and at ~390px. The countdown belongs above the fold on
   the phone, nothing should scroll sideways, and the map shouldn't tower over the text
   beside it. Above 1080px, check the band beside the hero — California's is sized for a
   five-hotel list, and a much shorter one leaves the hero looking stranded again.
6. Create a Vercel project pointed at the new trip's folder as its root directory.
7. Add the trip to the table at the end of this README.

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
