# AGENTS.md — vacation-itineraries

Repo-wide context for anyone (human or agent) working in here. Each trip folder has
its own `AGENTS.md` with that trip's specifics; read both.

## Hard rules

- **No confirmation numbers anywhere** — not on the page, not in the repo, not in a
  commit message. Airline record locators, hotel confirmations and rental booking
  numbers all stay in email. A record locator plus a last name is enough for a
  stranger to modify or cancel a booking.
- **Never enable Vercel deployment protection** on a trip project. Family members
  open these links without Vercel accounts.
- **No browser storage** in any dashboard. No `localStorage`, no `sessionStorage`.
- **Single file per trip.** If a change would require a build step, find another way.

## Editing a dashboard

The whole dashboard is `trips/<trip>/index.html`. Edit it in place and commit —
Vercel rebuilds on push. Don't paste whole-file contents through a deploy tool
unless git is unavailable; that was the old workflow and it was expensive.

Test by opening the file locally in a browser. There's nothing to build.

## Source of truth

Trip facts come from the user's own booking confirmations and screenshots, not from
summaries in these markdown files. Where a planning PDF and a later message
conflict, the later message wins, and the conflict gets recorded in the trip's
`AGENTS.md` so nobody "fixes" it back.

## Working preferences

- Flag conflicts and risks directly rather than smoothing them over. Tight
  connections, thin windows, unbooked nights and off-airport rental returns get
  called out, not buried.
- Keep prose tight. Tables and cards do the heavy lifting.
- Round numbers honestly and label estimates as estimates.
- Spell out acronyms on first use.
- Cost-sensitive about token usage. Don't redeploy twice when one pass will do;
  don't re-read context that's already in hand.

## Deliberate exclusions are deliberate

Each trip's `AGENTS.md` lists attractions that were cut on purpose to keep days from
feeling rushed. Do not helpfully add them back.
