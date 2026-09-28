# Spike SP-02 — Does a publish reach a reader on another network within sixty seconds?

- **Unknown:** Whether on-demand invalidation actually changes what the CDN edge serves to somebody else, and how long it takes.
- **Feeds:** ADR 0002 — deployment platform, and ADR 0004 — page rendering strategy. This is the highest-weighted criterion in both.
- **Requirements at risk:** FR-SYS-02, NFR-PERF-01, FR-HOME-02, FR-HOME-03, FR-IDX-01
- **Time box:** 2 hours — and you stop when it rings.
- **Run on:** 2026-10-01 _(requires SP-01's deployed app to exist first)_

## The question

After I change data through the deployed site, does a **different device on a different network** see that change on a pre-rendered page within sixty seconds, with no redeployment?

## The smallest thing that answers it

Reuse SP-01's deployed app. Add nothing except what the question needs.

1. Make `/spike` a pre-rendered page rather than one rendered per request. **Then check the build output and confirm the route is actually marked as static** — do not assume it. If it is being rendered per request, this spike measures nothing.
2. Add a second page, `/spike/count`, that renders only the number of rows.
3. In the server action that inserts a row, after the insert, call the revalidation API for **both** paths — `/spike` and `/spike/count`.
4. Push and wait for the deploy to finish.
5. On your **phone, over cellular data** (not your home wifi), open `/spike/count`. Write down the number.
6. On your desktop, submit a new row through the deployed form. Start a timer.
7. Reload on the phone every ten seconds. Write down how long it took for the number to change.
8. **Control run.** Comment out both revalidation calls. Push. Repeat steps 5 to 7.

Step 8 is the step people skip and it is the one that makes the result mean anything. If the page updates immediately even with revalidation removed, then the page was never cached, and step 7 measured nothing at all.

Why a different device on a different network: your own browser can be served a copy that was invalidated specifically for your request. A second device on a second network hits a different edge with a cold cache, which is what an actual reader gets.

## Success criterion

Two things, both required:

- The change appears on the second device in **60 seconds or less**.
- The control run shows the page **not** updating, proving the page was genuinely cached and that the first result measured real invalidation.

## Failure criterion

Any one of these means no:

- The change takes longer than 60 seconds.
- The control run also updates immediately — the page was never cached, so nothing was proven and the spike must be re-run after fixing the rendering mode.
- The revalidation call throws an error.
- One path updates and the other does not, which means FR-SYS-02's three-path requirement will silently half-work.

## Plan B if it fails

Fall back to time-based revalidation — declaring that the page may be up to sixty seconds stale and letting the platform regenerate it on that interval. This satisfies FR-SYS-02's ceiling without any explicit invalidation call, and it is less code.

If neither mechanism works, ADR 0002's highest-weighted criterion has failed on the platform I chose. That triggers the revisit condition already written into ADR 0002 and ADR 0004: Astro plus a short revalidation interval becomes the better trade, and both ADRs get superseded rather than amended.

## Result

_To be completed after the time box. Record both the measured time and the control-run behaviour — the control is half the finding._

## Decision

_Proceed, fall back to time-based revalidation, or re-spike. Then update ADR 0002 and ADR 0004, the risk register, and `docs/hours-log.csv`._

**What to keep from this spike:** throw the code away, but write the two-device procedure into `docs/measurements/` as the standing test for FR-SYS-02. You will need to run it again every time the caching behaviour changes, and FR-SYS-02 cannot be verified any other way.
