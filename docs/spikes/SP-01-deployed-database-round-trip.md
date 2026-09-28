# Spike SP-01 — Can a page on the deployed site write a row and then read it back?

- **Unknown:** Whether the database client works inside the platform's serverless runtime, and whether environment variables and migrations line up between my machine and production.
- **Feeds:** ADR 0001 — content persistence layer. Also tests the execution-model assumption in ADR 0002.
- **Requirements at risk:** FR-CONT-01, FR-CONT-02, FR-READ-01, FR-IDX-01, FR-CMT-01, NFR-PERF-02
- **Time box:** 2 hours — and you stop when it rings.
- **Run on:** 2026-09-30

## The question

Can I insert a row using a form on the **deployed** URL and then see that same row rendered on a public page of the **deployed** site?

## The smallest thing that answers it

A throwaway Next.js app with one table, one page, and one form. No auth, no validation, no styling, no error handling beyond printing what went wrong.

**Do not use the real essays schema.** This spike answers a plumbing question, and a throwaway table keeps the answer clean.

1. `npx create-next-app@latest spike-01` and accept every default.
2. `npm i @libsql/client`
3. Create a Turso database. Copy its URL and auth token.
4. Put both in `.env.local`. **Open `.gitignore` and confirm `.env.local` is listed** before you commit anything.
5. Write `migrate.mjs` — a plain script that creates one table:
   `spike_notes (id integer primary key, body text not null, created_at text default current_timestamp)`
6. Run `migrate.mjs` against your local database file. Then run it again with the environment pointed at the hosted database. Two runs, two targets.
7. Create `app/spike/page.jsx` as a server component: query every row, render them in a `<ul>`.
8. Add a form with one text input, posting to a server action that inserts the text and then refreshes the page.
9. Confirm it works on `localhost`. **This proves nothing yet** — it's just the checkpoint before the real test.
10. Push to `main`. In the Vercel dashboard, add the same two environment variables. Wait for the deploy.
11. Open the deployed `/spike` URL. Submit the form. Reload the page.

## Success criterion

A row submitted through the form on the deployed URL appears on the deployed page after a reload. Both directions — write and read — work in production, not just locally.

## Failure criterion

Any one of these means no:

- The database client throws an error in the deployed runtime (works locally, fails deployed).
- The page renders locally but returns a server error when deployed.
- The insert reports success but the read comes back empty — which means the two operations are hitting different databases.
- `migrate.mjs` cannot apply the table to the hosted database from the command line.

## Plan B if it fails

If the client throws in the deployed runtime, re-spike once with the route explicitly forced to the Node runtime rather than the edge runtime, before concluding anything — that is the single most likely cause and it is a one-line change.

If that also fails, ADR 0001 reopens and Neon becomes the leading option, since it ships a driver built for exactly this problem. Before switching, read the Neon free-tier row in `tech-evaluation.csv` and log it — that row is currently PROVISIONAL and would need to be settled first.

## Result

_To be completed after the time box. Record what actually happened, with the error text if there was one, and what surprised you._

## Decision

_Proceed, fall back, or re-spike with a narrower question. Then update ADR 0001's status, the risk register, and `docs/hours-log.csv`._

**What to keep from this spike:** throw the app away, but keep `migrate.mjs` and the exact environment-variable names. Write both into the README — they are the first thing NFR-MAINT-01's clean-machine test will need.
