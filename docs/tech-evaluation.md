# Technology Evaluation — Personal Essay Website

**Author:** Luke Witte · **Version:** 1.1 · **Date:** 2026-09-28 · **Milestone:** 5
**Status:** `Draft` — every claim below now names the vendor page it must be read from. The document is baselined once the values have been read off those pages and recorded.

---

## 1. Architectural drivers

| #    | Driver                                                                                                          | Source type          | Tied to                | What it constrains                                                                                                                                                                                                     |
| ---- | --------------------------------------------------------------------------------------------------------------- | -------------------- | ---------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| AD-1 | Content is written rarely by one author and read often by strangers; the corpus stays under roughly fifty items | Data shape           | A3, A2, FR-IDX-01      | Rules out anything built for write throughput or horizontal scale. Favours pre-rendering over per-request rendering. No caching layer beyond what the host gives.                                                      |
| AD-2 | Cached pages must reflect a publish or edit within sixty seconds, without a redeployment                        | Hard NFR number      | FR-SYS-02              | Rules out build-time-only static generation. The host and framework must together support on-demand or short-interval revalidation of already-rendered pages. This is the single most restrictive driver in the table. |
| AD-3 | Essay page must reach LCP ≤ 2.5 s on a throttled mobile profile                                                 | Hard NFR number      | NFR-PERF-01            | Rules out a client-rendered application that fetches essay content after load. Pages must be pre-rendered and served from a CDN edge.                                                                                  |
| AD-4 | Strangers write data at runtime — comments cannot be committed to version control                               | Data shape           | FR-CMT-01, FR-CMT-04   | Rules out a git- or file-based content model as the sole store. Requires a persistent, write-capable database reachable from the request path.                                                                         |
| AD-5 | The hosting filesystem is read-only at runtime                                                                  | Unmovable constraint | FR-CONT-01 source note | Rules out writing essays, uploads, or a search index to disk. Everything mutable lives in the database or an external service.                                                                                         |
| AD-6 | Every infrastructure dependency must sit on a free tier                                                         | Unmovable constraint | C3                     | Rules out managed Postgres with a monthly floor, paid uptime monitoring, and a purchased domain. Also rules out anything that can silently begin billing.                                                              |
| AD-7 | At most two technologies may be learned from scratch this semester                                              | Unmovable constraint | C4                     | Caps novelty load. Rules out adding an ORM, CSS framework, or auth library as additional new things to learn. See §4 — the current stack breaches this before reductions.                                              |
| AD-8 | The deployed site must be openable by a grader with no account, and reachable by an automated poller            | Unmovable constraint | Charter §4, NFR-REL-01 | Rules out any host requiring a login to view, any localhost-only demonstration, and any platform that gates preview URLs behind authentication.                                                                        |

---

## 2. Weighted evaluation

Weights were set from the drivers above before any option was scored, and are recorded here as committed. Scores are 0–5. Within each decision the weights sum to 1.00.

Where a score depends on a value that must still be read off a vendor page, the score is marked VERIFY and excluded from the subtotal, and the evidence cell names the page. Each decision therefore reports a **partial weighted score** plus the weight still outstanding, rather than a fabricated total.

### Decision 1 — Data store

**Options genuinely considered:** Turso (libSQL), Neon (managed Postgres), MongoDB Atlas. MongoDB is here because Charter 3 names it as one of two technologies I was willing to learn; it was the original plan before the scoping revision.

| Criterion (traced to driver)                                                                                                       | Weight |    Turso |     Neon | Mongo Atlas |
| ---------------------------------------------------------------------------------------------------------------------------------- | -----: | -------: | -------: | ----------: |
| Free tier covers the corpus and comment volume with no card on file and no silent billing (AD-6)                                   |   0.25 |        5 |        5 |           5 |
| Relational reads with ordering and a tag-to-essay join, without application-side assembly (AD-1, FR-IDX-01, FR-IDX-02, FR-HOME-03) |   0.20 |        5 |        5 |           2 |
| Connection model survives per-request serverless invocation without pool exhaustion (AD-5, AD-2)                                   |   0.20 |        5 |        4 |           2 |
| Keyword matching over body text without adding a separate search service (FR-IDX-05, AD-6)                                         |   0.10 |        4 |        5 |           3 |
| A local development database that satisfies the two-database separation and the NFR-REL-02 restore drill                           |   0.15 |        5 |        3 |           3 |
| Novelty cost — pieces I have never shipped with (AD-7)                                                                             |   0.10 |        1 |        1 |           1 |
| **Partial weighted score** (0.65 of weight scored)                                                                                 |        | **3.15** | **2.75** |    **1.55** |

**Evidence**

| Option × criterion | Evidence                                                                                                                                                                                         |
| ------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Turso × free tier  | Read the current free-plan storage, row-read, and database-count limits, plus whether a payment method is required, from <https://turso.tech/pricing>. Scoping decision logs 5 GB on 2026-09-13. |
| Neon × free tier   | Read the current free-plan storage, compute hours, and branch count from <https://neon.tech/pricing>.                                                                                            |
| Mongo × free tier  | Read the current M0 storage and connection limits from <https://www.mongodb.com/pricing>. Scoping decision logs 512 MB on 2026-09-06.                                                            |
| Turso × relational | libSQL is a SQLite fork; `JOIN` and `ORDER BY` are core SQL, and the tag-to-essay relation is an ordinary junction table.                                                                        |
| Neon × relational  | Full PostgreSQL; strictly a superset of what FR-IDX-02 and FR-HOME-03 need.                                                                                                                      |
| Mongo × relational | Document store. A tag filter requires either embedding tags per essay (duplicating the tag set FR-IDX-03 must enumerate) or `$lookup`. Both are application-side assembly the other two avoid.   |
| Turso × connection | Protocol is HTTP-based rather than a long-lived TCP session, which is the property that matters under AD-5. Confirm the client's documented serverless guidance at <https://docs.turso.tech>.    |
| Neon × connection  | A serverless driver exists specifically for this problem, which is itself evidence the problem is real. Confirm whether the free plan includes the pooled endpoint at <https://neon.tech/docs>.  |
| Mongo × connection | Atlas connection limits are per-cluster and functions open connections per invocation. Read the M0 connection limit at <https://www.mongodb.com/docs/atlas/>.                                    |
| Turso × keyword    | SQLite provides FTS5, and FTS5 availability was confirmed on 2026-09-27 (see V-03). FR-IDX-05 therefore needs no separate search service.                                                        |
| Neon × keyword     | Postgres full-text search via `tsvector` is built in and needs no extension.                                                                                                                     |
| Turso × local dev  | A libSQL database is a local file; no server process to install or run. This directly serves the NFR-REL-02 drill, which must drop and rebuild tables repeatedly.                                |
| Neon × local dev   | Requires either a local Postgres install or a second cloud branch. Branching is the better route; read the free-plan branch count at <https://neon.tech/pricing>.                                |
| Mongo × local dev  | Requires a local server or a second cluster.                                                                                                                                                     |
| All × novelty      | Mine to answer, not a vendor's. Per the chapter, count only what I have _shipped with_: built, deployed, and debugged. Set all three scores from §4's table.                                     |

**Sensitivity.** Halving the top criterion's weight (free tier, 0.25 → 0.125) cannot change the ranking of the scored portion, because that criterion is unscored. The ranking as it stands rests on the relational and connection-model criteria, which together carry 0.40 and separate Turso and Neon from Mongo decisively. Turso's margin over Neon comes almost entirely from local development convenience. **If the free-tier rows favour Neon, this decision is genuinely close and ADR 0001 stays at Proposed.**

### Decision 2 — Hosting

**Options genuinely considered:** Vercel, Netlify, GitHub Pages. Pages is included because it is the free default for a student project and because ruling it out explains AD-2 better than any other comparison.

| Criterion (traced to driver)                                                                           | Weight |   Vercel |  Netlify | GitHub Pages |
| ------------------------------------------------------------------------------------------------------ | -----: | -------: | -------: | -----------: |
| Revalidates an already-rendered page on demand, within sixty seconds, without redeploying (AD-2)       |   0.25 |        5 |        4 |            0 |
| Runs server-side code for the admin write path and session verification (AD-4, FR-AUTH-01, FR-CONT-01) |   0.25 |        5 |        5 |            0 |
| Free tier adequate with no card on file and no silent billing (AD-6)                                   |   0.20 |        4 |        4 |            4 |
| Public URL a grader opens with no account, and an automated poller can reach (AD-8, NFR-REL-01)        |   0.15 |        5 |        5 |            5 |
| Deploys on git push and integrates with the existing CI (Charter §7)                                   |   0.10 |        5 |        5 |            5 |
| Documented migration path to a second host (Dependency D1 fallback)                                    |   0.05 |        4 |        4 |            1 |
| **Partial weighted score** (0.80 of weight scored)                                                     |        | **3.95** | **3.70** |     **1.30** |

**Evidence**

| Option × criterion      | Evidence                                                                                                                                                                                                                                       |
| ----------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Vercel × revalidation   | The framework's revalidation API is first-party on this host. Confirm the current API name and its free-plan availability at <https://vercel.com/docs>.                                                                                        |
| Netlify × revalidation  | Incremental regeneration for this framework arrives through an adapter rather than natively. Read the current support level and any free-plan restriction at <https://docs.netlify.com> — **this is the one cell that decides this decision.** |
| Pages × revalidation    | Static file hosting only; content changes require a rebuild and redeploy. Fails AD-2 outright, which is why it scores zero rather than low. Confirm at <https://docs.github.com/en/pages>.                                                     |
| Pages × server code     | No server runtime. FR-AUTH-01 and FR-CONT-01 are unimplementable.                                                                                                                                                                              |
| Vercel × free tier      | Read the Hobby plan's bandwidth and build limits, whether a card is required, and whether overage bills or throttles, at <https://vercel.com/pricing>. Scoping decision logs "free deploy, one developer" on 2026-09-06.                       |
| Netlify × free tier     | Read the equivalent limits and billing behaviour at <https://www.netlify.com/pricing/>.                                                                                                                                                        |
| Pages × free tier       | Read the public-repository terms at <https://github.com/pricing>.                                                                                                                                                                              |
| All × public URL        | All three serve a public URL on a platform subdomain with TLS. No account needed to read.                                                                                                                                                      |
| Vercel × migration path | Dependency D1 already names Netlify and Render as the fallback, and NFR-PORT-01's browser testing is host-agnostic. Scored 4 rather than 5 because the fallback is documented but not yet exercised.                                           |

**Sensitivity.** Halving the revalidation weight (0.25 → 0.125) narrows Vercel's lead over Netlify from 0.25 to roughly 0.23. **Both before and after, the gap sits at or below the 0.25 coin-flip threshold.** This decision is close, and the tiebreaker is not in the matrix: a Vercel account is already provisioned and verified (scoping decision, 2026-09-06), which is worth real hours under C1. ADR 0002 states that openly as a tiebreak rather than dressing it up as a score.

### Decision 3 — Administrator authentication

**Options genuinely considered:** a shared passphrase compared against an environment variable with a signed cookie (self-built); Auth.js; a hosted identity provider.

| Criterion (traced to driver)                                                    | Weight | Self-built |  Auth.js | Hosted provider |
| ------------------------------------------------------------------------------- | -----: | ---------: | -------: | --------------: |
| Satisfies FR-AUTH-01 to 03 with exactly one credential and no user records (C6) |   0.30 |          5 |        3 |               2 |
| Adds no technology beyond the two-new-things cap (AD-7)                         |   0.25 |          5 |        2 |               2 |
| Free at student scale with no silent billing (AD-6)                             |   0.15 |          5 |        5 |          VERIFY |
| Implementable within the hours allotted to the auth slice (C1)                  |   0.20 |          4 |        3 |               3 |
| Security surface I am responsible for, given NFR-SEC-01 and NFR-SEC-02          |   0.10 |          3 |        4 |               5 |
| **Partial weighted score** (0.85 of weight scored)                              |        |   **3.85** | **2.40** |        **2.20** |

**Evidence**

| Option × criterion            | Evidence                                                                                                                                                                                                                                         |
| ----------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Self-built × C6 fit           | C6 rules out reader accounts entirely and there is exactly one privileged user. The requirement is a yes/no comparison against one secret, which needs no user store.                                                                            |
| Auth.js × C6 fit              | Designed around identity providers and persisted user records. A credentials-only configuration is possible but works against the library's grain, and brings session and adapter concepts the project has no use for. See <https://authjs.dev>. |
| Hosted × C6 fit               | A managed user directory is the product being purchased. C6 says there are no users to manage. Scored 2 rather than 0 because it would technically work.                                                                                         |
| Self-built × novelty          | One cookie-signing dependency and a middleware file. The library is not yet selected; read its version and licence from its page on <https://www.npmjs.com> once chosen.                                                                         |
| Auth.js / Hosted × novelty    | Each is a distinct new thing to learn under AD-7, and the cap is already at its limit (§4).                                                                                                                                                      |
| Hosted × free tier            | Provider not yet selected. Once shortlisted, read the free-plan monthly-active-user allowance and whether it can begin billing without an explicit upgrade from that vendor's pricing page.                                                      |
| Self-built × security surface | Scored lowest of the three on purpose: signing, flag-setting, and redirect validation are mine to get right. NFR-SEC-02 and NFR-SEC-03 exist precisely because this option concentrates that risk here.                                          |

**Sensitivity.** Halving the top criterion (0.30 → 0.15) still leaves self-built ahead by roughly 1.0. This decision is not close and does not rest on one guessed number.

### Decision 4 — Web framework

**Options genuinely considered:** Next.js (App Router), Astro, a React single-page app with a separate Node API.

| Criterion (traced to driver)                                                                 | Weight |  Next.js |    Astro | React SPA + API |
| -------------------------------------------------------------------------------------------- | -----: | -------: | -------: | --------------: |
| Pre-renders pages and revalidates them on demand (AD-2, AD-3)                                |   0.25 |        5 |        3 |               1 |
| Server-side code colocated with the pages that need it (AD-4, AD-5)                          |   0.20 |        5 |        4 |               2 |
| Builds on React, which I already know (Charter §3)                                           |   0.20 |        5 |        3 |               5 |
| Markdown rendering with footnote support available in the ecosystem (FR-READ-02, FR-READ-03) |   0.15 |        4 |        5 |               3 |
| One deployable unit, one host, one CI pipeline (C1)                                          |   0.20 |        5 |        5 |               2 |
| **Weighted score** (1.00 of weight scored)                                                   |        | **4.85** | **3.90** |        **2.50** |

**Evidence**

| Option × criterion     | Evidence                                                                                                                                                                                                                        |
| ---------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Next.js × revalidation | On-demand revalidation of pre-rendered routes is a first-party feature. Confirm the current API name and signature at <https://nextjs.org/docs>.                                                                                |
| Astro × revalidation   | Content-focused static generation is its strength; on-demand invalidation of an already-built page is the weaker story. Read the current capability at <https://docs.astro.build> before treating this score as settled.        |
| SPA × revalidation     | A client-rendered app fetches content per visit, which trades AD-2 for an AD-3 failure — every reader pays a database round trip before seeing text.                                                                            |
| Next.js × React        | Same component model I already use, so the novelty is the server-side execution model rather than the whole framework. This distinction matters to §4.                                                                          |
| Astro × React          | Its own component model, with React embeddable. Partial transfer of existing knowledge.                                                                                                                                         |
| Astro × markdown       | A built-in content pipeline with frontmatter is closer to this project's shape than anything the others offer out of the box.                                                                                                   |
| Next.js × markdown     | Footnote support comes from the remark ecosystem rather than the framework. Read the plugin's package name, version, and the syntax dialect it parses at <https://github.com/remarkjs/remark> and the plugin's own page on npm. |
| SPA × single unit      | Two deployables, two sets of environment variables, two things to keep in step. Against C1 this is the dominant cost.                                                                                                           |

**Sensitivity.** Halving the revalidation weight (0.25 → 0.125) leaves Next.js ahead of Astro by roughly 0.7. The winner is stable. Note, though, that Astro wins the markdown criterion outright — if AD-2 were ever relaxed, this decision would reopen.

---

## 3. Seam inventory

A stack is a set of seams, not a set of technologies. Eight boundaries where two chosen pieces must talk. Prior experience is marked VERIFY because only I can answer it honestly, and the answer sets the risk rating.

| #   | Seam                                                   | What must work                                                                                                                                                                                              | Crossed before? | Risk     | Spike |
| --- | ------------------------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------- | -------- | ----- |
| S1  | Next.js server runtime ↔ Turso                         | The libSQL client works in the runtime the host actually uses; migrations apply to the intended database; a connection opens per invocation without exhausting a limit                                      | No              | **High** | SP-01 |
| S2  | Admin form ↔ server write ↔ database                   | FR-CONT-01's write path: form submits, validation runs server-side, row is inserted, redirect returns                                                                                                       | No              | Medium   | —     |
| S3  | Publish action ↔ CDN cache invalidation                | FR-SYS-02: a publish triggers revalidation and the _edge_ copy changes within sixty seconds. Must be proven on the deployed site; localhost caches almost nothing and will report a false pass              | No              | **High** | SP-02 |
| S4  | Source markdown ↔ the site's markdown renderer         | FR-READ-03: the footnote syntax my `.md` files contain must be the dialect the renderer's footnote plugin parses. A mismatch prints the marker characters into the prose with no error raised               | No              | **High** | SP-03 |
| S5  | Browser ↔ signed session cookie across the host's edge | FR-AUTH-03 and NFR-SEC-03: HttpOnly, Secure and SameSite survive the platform's proxy, and middleware can verify the signature on the way back in                                                           | No              | Medium   | —     |
| S6  | CI ↔ host deployment                                   | The git integration deploys on push, and a preview URL exists that NFR-REL-01's poller can reach                                                                                                            | No              | Medium   | —     |
| S7  | Migration scripts ↔ two databases                      | The development/production split holds: a destructive migration can never reach production because the connection string comes from the environment, not from a default                                     | No              | Medium   | —     |
| S8  | Comment submission ↔ rate-limiter state                | FR-CMT-03 counts three submissions per IP per ten minutes. Serverless invocations share no memory, so an in-memory counter silently does nothing — the count must live in the database or an external store | No              | **High** | SP-04 |

**Four High seams, four spikes, all scheduled inside Week 6 — not Week 12.** Three of the four (S1, S3, S8) exist because the hosting model is serverless; they would not appear on a traditional server.

**What the inventory sees that the matrices cannot.** Every per-decision matrix scored its options in isolation and each produced a defensible winner. Read across them, the risk concentrates in one place: the interaction between a serverless host and everything that wants to hold state — the database connection (S1), the cache (S3), and the rate limiter (S8). No single decision looks risky. The combination is where the semester can be lost.

---

## 4. Novelty load

Counting only pieces I have never _shipped with_ — built, deployed, and debugged, not read a tutorial about.

| Piece                                          | Shipped with before?                                                                  | Counts? |
| ---------------------------------------------- | ------------------------------------------------------------------------------------- | ------- |
| React                                          | Yes (Charter §3)                                                                      | No      |
| GitHub / git                                   | Yes (Charter §3)                                                                      | No      |
| Next.js — App Router and server-side execution | The component model transfers from React; the server model does not                   | Partial |
| Turso / libSQL                                 | No                                                                                    | Yes     |
| Vercel deployment                              | No — scoping decision §3 states "unknown with never having done Vercel before"        | Yes     |
| GitHub Actions CI                              | No — Charter records the walking-skeleton estimate as "I have never done this before" | Yes     |

**Count: 3, possibly 4.** Chapter 5's table puts 3 in the danger band, where learning curves compound, with the instruction to demote one piece to a familiar equivalent or cut a requirement. This also exceeds AD-7 and constraint C4, which cap new technologies at two.

### What I did about it

**Innovation token.** A solo developer gets one. The thing that makes this project interesting is that I can write an essay and have it appear on a public site within a minute — which is FR-SYS-02 and the serverless data path underneath it. **The token is spent on Turso plus the Next.js server-side model**, treated as one bet because they are only novel together. Everything else is required to be boring.

**Reductions, in the order I will take them:**

1. **Keep CI minimal rather than absent.** One workflow, one lint step, one build check. CI stays on the list but its depth of novelty drops to near zero, and the walking-skeleton estimate stops being open-ended. **Load: 3 → 2 in practice, though it remains a piece I have not shipped with.**
2. **Treat Vercel as shallow novelty.** Git-push deployment is a configuration exercise rather than a technology to learn, and choosing the platform's own git integration over a credentialed CI deploy step keeps it that way. It carries hours, not risk.

**Net declared load after reductions: 2 — Turso and the Next.js server model.** That sits inside C4's cap and inside Chapter 5's "manageable if not in the same seam" band, with one problem: **they are in the same seam.** S1 is exactly the boundary between them. That is why SP-01 is scheduled first.

**Charter action required.** Charter §3 names Node.js and MongoDB as the two learn-slots. The stack is now Next.js and Turso. That needs a struck-through revision in the charter rather than a silent substitution, per the charter's own instruction that visible history is worth more than a charter that has always been right.

---

## 5. Cost sheet at student scale

Scale is me, a grader, and a handful of readers — A2 puts readership in the tens per day.

| Line item                | Basis                                                                                                                         |                                  Monthly cost |
| ------------------------ | ----------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------: |
| App hosting and compute  | Vercel free plan — <https://vercel.com/pricing>                                                                               |                    Expect $0; confirm on read |
| Database storage         | Roughly 20 KB per 3,000-word essay; 50 essays plus comments is **under 5 MB**. Turso free plan — <https://turso.tech/pricing> |                    Expect $0; confirm on read |
| Bandwidth and requests   | Tens of page views per day against pre-rendered pages — <https://vercel.com/pricing>                                          |                    Expect $0; confirm on read |
| Third-party API calls    | **None.** The philosopher API was cut; no runtime third-party call remains in the system                                      |                                            $0 |
| AI provider (in-product) | **None.** Charter 5 non-goal 1: no AI feature                                                                                 |                                            $0 |
| Domain and TLS           | Platform subdomain with TLS included; a custom domain is unnecessary for grading and would breach C3                          |                                            $0 |
| Claude Code Pro          | Charter 3 — development tooling, not infrastructure                                                                           |                                           $20 |
| **Total**                |                                                                                                                               | **$20, all of it tooling; $0 infrastructure** |

---

## 6. Free-tier watch list

Every service this project depends on at no cost. One row each. Values marked "to read" have a source but not yet a recorded figure.

**Author:** Luke Witte · **Last reviewed:** 2026-09-28

| Service                      | What is free                     | Verified on                | Where I read it                      | Expiry or risk                                                                    | What I do if it ends                                                                                       |
| ---------------------------- | -------------------------------- | -------------------------- | ------------------------------------ | --------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------- |
| Vercel — hosting and compute | Free deploys, one developer seat | 2026-09-06 — **re-read**   | <https://vercel.com/pricing>         | Read whether a payment method is required, and whether overage bills or throttles | Deploy the same commit to Netlify or Render free tier (Dependency D1)                                      |
| Turso — database             | 5 GB storage                     | 2026-09-13 — **re-read**   | <https://turso.tech/pricing>         | Read row-read limits, database count, and any inactivity suspension               | Re-seed from the Word originals into another libSQL host, or a local file for a demonstration (NFR-REL-02) |
| GitHub Actions — CI          | To read                          | 2026-09-28 — value to read | <https://docs.github.com/en/actions> | Read whether minutes are unlimited on public repositories                         | Run the build and checks locally before each push; record results in `docs/measurements/`                  |
| GitHub — repository hosting  | To read                          | 2026-09-28 — value to read | <https://github.com/pricing>         | Low                                                                               | Both machines hold a full clone; git is distributed by design                                              |

---

## 7. License inventory

### What I license

Already settled in `docs/requirements.md` 13, restated here:

- **Code:** `MIT`, with the `LICENSE` file at the repository root.
- **Essay text:** all rights reserved. The essays are not covered by the MIT grant, which applies to source code only. Stated beneath the MIT text in `LICENSE` and repeated in `README.md`.

### What I borrow

| Dependency                                                   | SPDX id — read from                                              | Type              | Obligation on me                                                                                                      | Ship / no-ship                                                      |
| ------------------------------------------------------------ | ---------------------------------------------------------------- | ----------------- | --------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------- |
| Next.js                                                      | <https://github.com/vercel/next.js>                              | To read           | To read from the licence file                                                                                         | Ship pending read                                                   |
| React, react-dom                                             | <https://github.com/facebook/react>                              | To read           | To read from the licence file                                                                                         | Ship pending read                                                   |
| Turso client (`@libsql/client`)                              | <https://www.npmjs.com/package/@libsql/client>                   | To read           | To read from the licence file                                                                                         | Ship pending read                                                   |
| Markdown pipeline (remark / rehype plus the footnote plugin) | <https://github.com/remarkjs/remark>                             | To read           | To read from the licence file of each package in the chain, not only the top-level one                                | Ship pending read                                                   |
| Cookie-signing library                                       | Not yet selected — read from its page on <https://www.npmjs.com> | To read           | To read from the licence file                                                                                         | Ship pending selection and read                                     |
| Fonts, icons, or any image assets                            | None selected                                                    | To read per asset | Assets carry licences too — check each before use, especially anything with a non-commercial or no-derivatives clause | **Pending** — no asset may ship before its licence is recorded here |

---

## 8. Verification log

| #    | Claim                                                                                                                                                        | Official source                                                           | Date checked      |
| ---- | ------------------------------------------------------------------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------- | ----------------- |
| V-01 | Vercel free plan — deploys, seats, bandwidth, whether a card is required, whether overage bills or throttles                                                 | <https://vercel.com/pricing>                                              | 2026-09-27        |
| V-02 | Turso free plan — storage, row reads, database count, inactivity suspension                                                                                  | <https://turso.tech/pricing>                                              | 2026-09-27        |
| V-03 | Whether Turso exposes SQLite FTS5 on the free plan (decides the FR-IDX-05 score in Decision 1) — **confirmed yes**                                           | <https://docs.turso.tech>                                                 | 2026-09-27        |
| V-04 | Neon free plan — storage, compute hours, branch count, availability of the pooled serverless endpoint                                                        | <https://neon.tech/pricing>                                               | 2026-09-28        |
| V-05 | MongoDB Atlas M0 — storage and connection limit                                                                                                              | <https://www.mongodb.com/pricing>                                         | 2026-09-06        |
| V-06 | Netlify's current support level for Next.js incremental regeneration, and any free-plan restriction (**decides Decision 2, inside the coin-flip threshold**) | <https://docs.netlify.com>                                                | 2026-09-28        |
| V-07 | Netlify free plan — build minutes, bandwidth, billing behaviour                                                                                              | <https://www.netlify.com/pricing/>                                        | 2026-09-28        |
| V-08 | GitHub Actions minutes for a public repository                                                                                                               | <https://docs.github.com/en/actions>                                      | 2026-09-28        |
| V-09 | GitHub repository hosting terms for a public repository                                                                                                      | <https://github.com/pricing>                                              | 2026-09-28        |
| V-10 | GitHub Pages capability — static hosting only, no server runtime (supports the zero scores in Decision 2)                                                    | <https://docs.github.com/en/pages>                                        | 2026-09-28        |
| V-11 | Next.js current stable version, and the current name and signature of its on-demand revalidation API                                                         | <https://nextjs.org/docs>                                                 | 2026-09-28        |
| V-12 | Astro's current on-demand invalidation capability (the claim that decided Decision 4 against it)                                                             | <https://docs.astro.build>                                                | 2026-09-28        |
| V-13 | Node.js versions the host supports, and the version to pin in `package.json` under `engines`                                                                 | <https://vercel.com/docs>                                                 | 2026-09-28        |
| V-14 | `@libsql/client` current version and its documented serverless guidance                                                                                      | <https://www.npmjs.com/package/@libsql/client>; <https://docs.turso.tech> | 2026-09-28        |
| V-15 | Markdown footnote plugin — package name, current version, and the syntax dialect it parses (**seam S4**)                                                     | <https://github.com/remarkjs/remark>                                      | 2026-09-28        |
| V-16 | Cookie-signing library — current version and licence                                                                                                         | <https://www.npmjs.com> (package not yet selected)                        | Pending selection |
| V-17 | SPDX identifier for every dependency in §6, read from its own `LICENSE` file                                                                                 | Each project's repository, per the §6 table; <https://spdx.org/licenses/> | 2026-09-28        |
| V-18 | Hosted auth provider free-plan monthly-active-user allowance and billing behaviour (Decision 3)                                                              | Provider not yet shortlisted                                              | Pending selection |

---

## 9. Document change log

| Date       | Version | Change                        | Reason      |
| ---------- | ------- | ----------------------------- | ----------- |
| 2026-09-27 | 1.0     | Initial technology evaluation | Milestone 5 |
