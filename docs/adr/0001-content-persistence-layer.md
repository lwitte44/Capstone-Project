# ADR 0001 — Content persistence layer

- **Status:** Proposed
- **Date:** 2026-09-28
- **Decider:** Luke Witte
- **Requirements affected:** FR-CONT-01 … FR-CONT-08, FR-CMT-01 … FR-CMT-07, FR-IDX-01 … FR-IDX-05, FR-HOME-01 … FR-HOME-03, FR-READ-01, FR-READ-05, FR-SYS-01, FR-SYS-02, NFR-PERF-02, NFR-REL-02
- **Related ADRs:** 0002 (the deployment platform constrains which connection models are viable), 0004 (the rendering strategy determines when reads happen)

## Context

The original plan read essays from a folder of markdown files committed to the repository. That plan died when the hosting model turned out to expose a read-only filesystem at runtime — FR-CONT-01's source note records the revision. Content has to live somewhere writable, and that somewhere is not the repository.

Two requirement groups then force a real database rather than a workaround. FR-CONT-01 through FR-CONT-08 could in principle still be satisfied by committing files, since I am the only author and I do have a commit workflow. FR-CMT-01 through FR-CMT-07 cannot. Comments are written by strangers at request time, and there is no version-control workflow that accommodates a stranger. That is the requirement that makes a persistence layer non-optional rather than merely convenient.

The shape of the data is small and relational. A3 assumes fewer than fifty essays, all text, no media. FR-IDX-02 filters by tag, FR-IDX-01 orders by publication date, FR-HOME-03 groups by year — ordinary joins and `ORDER BY` over at most a few thousand rows. At roughly 20 KB per three-thousand-word essay, the entire corpus plus comments is under 5 MB. Nothing about the volume is interesting, which means capacity is not what narrows the field.

What narrows the field is billing risk and learning cost. C3 puts the whole project budget into one thing, so every dependency must sit on a free tier and, more importantly, must not be able to begin billing without an explicit decision. C4 caps new technologies at two for the semester, and this decision spends one of those two slots. Charter 3 originally named MongoDB as one of the slots, which is why it appears below as a genuinely considered option rather than a straw man.

One further force comes from the host. The database is reached from short-lived serverless invocations that share no memory and no connections. A store whose client assumes a long-lived pooled session is therefore a liability in this setting rather than a mature feature.

## Options considered

| Option                  | Weighted score | The detail that decided it                                                                                                                                                                                                                                                            |
| ----------------------- | -------------: | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Turso (libSQL)          |           3.15 | The client talks over HTTP rather than holding a TCP session, so there is no connection pool to exhaust from a serverless invocation — and a local libSQL file is a development database with no server to install, which is what makes NFR-REL-02's restore drill cheap to rehearse. |
| Neon (managed Postgres) |           2.75 | Full PostgreSQL and the strongest text search of the three, but it ships a purpose-built serverless driver precisely because pooled connections are a problem in this setting. The existence of that driver is the evidence.                                                          |
| MongoDB Atlas           |           1.55 | A tag filter needs either `$lookup` or tags embedded per essay, and FR-IDX-03 must separately enumerate the distinct tag set — application-side assembly that a junction table avoids in both other options.                                                                          |

Scores are from `docs/tech-evaluation.md` Decision 1. They represent 0.65 of the total weight; the free-tier criterion (0.25) and the novelty criterion (0.10) are unscored pending the verification table below. **If the free-tier rows favour Neon, this decision is genuinely close and the status stays Proposed.**

## Decision

I will use Turso (libSQL) as the single store for essays, tags, and comments, with a local libSQL file as the development database and a separate hosted database for production, selected by the `DATABASE_URL` environment variable. Schema changes are tracked as migration files committed to the repository. I am not taking MongoDB despite Charter §3 naming it as a learn-slot, and the reason is FR-IDX-02 and FR-IDX-03's tag relation rather than a preference between the two.

## Consequences

**Positive**

- FR-CMT-01 through FR-CMT-07 become implementable at all. Comments get a home that version control structurally cannot provide.
- NFR-REL-02's four-hour restore drill is cheap to rehearse, because the development database is a file that can be deleted and rebuilt in seconds rather than a server to reprovision.
- FR-IDX-02 and FR-HOME-03 are ordinary SQL, so there is no application-side grouping or joining code to write, test, or get wrong.
- FR-CMT-03's rate limit needs no additional service. The `comments` table already holds `ip_hash` and `created_at`, so the ten-minute count is a query against data the schema carries anyway.
- FR-CONT-07's draft state and FR-CONT-08's featured flag are single columns with a `WHERE` clause, rather than a convention about which folder a file sits in.

**Negative**

- This spends one of the two new technologies C4 permits. Budget: **VERIFY** hours for a first working read-and-write path, inside the walking-skeleton estimate.
- The boundary between the runtime and this database is the highest-risk integration point in the project. A missing environment variable in production, or migrations applied to the development database while the deployed code expects the production schema, produces a site that builds successfully and fails on every request. Mitigation: prove a write-then-read round trip on the deployed URL before building anything on top of it. Cost: one spike session in Weeks 5–7.
- All content now lives in one hosted service, so an outage there means every page fails rather than degrading gracefully. Mitigation: the essays exist as personal originals outside the system, and a seed script rebuilds from them. Cost: the seed script, plus the NFR-REL-02 drill in Week 13.
- Two databases must be kept rigorously distinct, and a migration script with a default connection string is how production data gets dropped at eleven at night. Mitigation: the migration script exits with an error when `DATABASE_URL` is unset rather than falling back to anything, and prints the target database name before any destructive operation. Cost: three lines, and the discipline not to remove them later.
- FR-IDX-05's keyword search may fall back to `LIKE` rather than a real full-text index, depending on what the platform exposes. At fifty essays that is not a user-visible difference, but it is a limit I am accepting on an unverified assumption rather than measuring.

## Revisit trigger

- If the free plan's storage, row-read, or database-count limit is exceeded, or the plan begins requiring a payment method without a hard spend cap.
- If the corpus passes **200 essays**, at which point A3 is false, FR-HOME-03's year-grouped sidebar stops being navigable, and FR-IDX-05 needs a real index rather than a scan.
- If A1 turns false and a second author gains write access, changing the access pattern from single-writer to concurrent and invalidating the assumption that no write ever contends with another.

## Verification

| Claim in this ADR                                                                                   | Source                                    | Checked on                |
| --------------------------------------------------------------------------------------------------- | ----------------------------------------- | ------------------------- |
| Turso free plan — storage, row reads, database count, inactivity suspension                         | VERIFY — vendor pricing page              | 2026-09-13, **re-verify** |
| Whether the platform exposes SQLite FTS5 on the free plan (decides the FR-IDX-05 consequence above) | VERIFY — vendor documentation             | VERIFY                    |
| `@libsql/client` current version and its documented serverless guidance                             | VERIFY — package registry and vendor docs | VERIFY                    |
| Neon free plan — storage, compute hours, branch count, availability of the pooled endpoint          | VERIFY — vendor pricing page              | VERIFY                    |
| MongoDB Atlas M0 — 512 MB storage, connection limit                                                 | Scoping decision dependency table         | 2026-09-06                |
