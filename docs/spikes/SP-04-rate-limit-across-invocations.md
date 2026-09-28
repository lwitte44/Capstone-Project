# Spike SP-04 — Does per-address rate limiting actually block anything in production?

- **Unknown:** Whether a rate limit can work at all on this platform, given that serverless invocations share no memory — and whether the visitor's address is even available behind the CDN.
- **Feeds:** ADR 0001 — content persistence layer, specifically the claim that the comments table needs no additional service to satisfy FR-CMT-03.
- **Requirements at risk:** FR-CMT-03, FR-CMT-01, and the login-rate-limit gap noted in ADR 0003
- **Time box:** 90 minutes — and you stop when it rings.
- **Run on:** 2026-10-04 _(requires SP-01's deployed app to exist first)_

## The question

On the deployed site, does the fourth submission from one device inside ten minutes get refused, and a submission eleven minutes later get accepted?

## The smallest thing that answers it

Reuse SP-01's deployed app. No comment interface, no moderation queue, no length checking — this question is about counting, nothing else.

1. Find out **how the runtime exposes the visitor's address.** Behind a CDN it arrives as a request header, not as a socket property. Look this up before writing anything; guessing wastes the time box.
2. Add a throwaway table: `spike_hits (addr_hash text, created_at text)`.
3. In the server action: hash the address with a salt from an environment variable, count rows for that hash created in the **last ten minutes**, refuse if the count is already 3, otherwise insert a hit row and the note.
4. Deploy.
5. From your phone, submit four times in a row. **The fourth must be refused.**
6. **Control run.** Now deliberately build the naive version — a plain counter held in a variable at the top of the file, incremented per request, with no database. Deploy it. Submit four times.
7. Wait eleven minutes. Submit once more against the database version. It must be accepted.

Step 6 is worth the ten minutes it costs. It demonstrates the failure mode rather than asking you to take it on trust — the in-memory counter will work perfectly on your laptop and do nothing at all in production, and seeing that happen once is what stops you writing it by accident in Week 11.

Note in step 3: measure the window **backwards from now**, not as a fixed clock interval. A limit that resets on the hour lets four submissions through at 10:59 and four more at 11:01.

## Success criterion

All three, together:

- The database version refuses the fourth submission inside ten minutes.
- The eleventh-minute submission is accepted.
- The in-memory control version **demonstrably fails to block**, confirming the seam is real and that the database version is doing the work.

## Failure criterion

Any one of these means no:

- The database version does not block — the count query or the window arithmetic is wrong.
- The address header is missing, or returns the same value for every request, which would make per-address limiting impossible on this platform.
- The stored value is a readable address rather than a hash, which would violate the privacy position in requirements §6.

## Plan B if it fails

If the address is unavailable or identical across requests, FR-CMT-03 cannot be implemented as written. Three options, in order of preference:

1. Rewrite FR-CMT-03 as a **global** submission rate — say twenty comments per hour across the whole site — which needs no per-visitor identity at all.
2. Add a honeypot field: a hidden input that humans leave empty and simple bots fill in. Cheap, no identity needed, and catches the crudest traffic.
3. Drop FR-CMT-03 to Won't and rely on FR-CMT-04's moderation queue alone.

Option 3 is more defensible than it first looks: nothing reaches a reader without your approval anyway, so the queue is already the real defence. Rate limiting protects your database and your attention, not your readers. Say that in the ADR if you take it.

## Result

_To be completed after the time box. Record which header carried the address, what the control run did, and the measured behaviour at the ten- and eleven-minute marks._

## Decision

_Proceed, rewrite FR-CMT-03, or drop it. Then update ADR 0001's consequences, FR-CMT-03's priority if it changed, and `docs/hours-log.csv`._

**What to keep from this spike:** keep the hashing helper and the count query — both go straight into the real FR-CMT-03 implementation. Also keep the answer to step 1 written down, because ADR 0003's login rate limit will need the same header.
