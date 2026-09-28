# ADR 0002 — Deployment platform

- **Status:** Proposed
- **Date:** 2026-09-28
- **Decider:** Luke Witte
- **Requirements affected:** FR-SYS-02, FR-AUTH-01 … FR-AUTH-04, FR-CONT-01 … FR-CONT-03, NFR-PERF-01, NFR-PERF-03, NFR-REL-01, NFR-PORT-01
- **Related ADRs:** 0001 (the platform's execution model constrains the database connection model), 0004 (the platform must honour the invalidation the framework requests)

## Context

FR-SYS-02 is the most restrictive requirement in the specification, and it is a hosting requirement as much as a code one. It says a publish or edit must be visible to a reader within sixty seconds, without a redeployment. Any host that only serves files produced at build time cannot satisfy it, because there is no mechanism to change what is being served without producing new files.

NFR-PERF-01 pulls in the opposite direction. LCP of 2.5 seconds or less on a throttled mobile profile rules out rendering every page on request, because that makes every reader wait on a database round trip before any text appears. The only strategy that satisfies both is to pre-render pages and selectively invalidate them — and invalidation happens at the CDN edge, which is the host's territory rather than the framework's. A framework can express the intent to invalidate a path; if the platform does not implement it at the edge, the intent is inert. That is why this criterion carries the highest weight here and in ADR 0004.

FR-AUTH-01 through FR-AUTH-04 and FR-CONT-01 need server-side execution in the request path. A static file host cannot compare a passphrase against an environment variable or insert a row, so an entire category of otherwise-free hosting is disqualified on capability rather than on cost.

Two constraints narrow what remains. Charter §4 requires that a stranger can open the deployed site, and NFR-REL-01 polls three public URLs on a schedule — both rule out anything gated behind a login or a preview token. C3 puts the entire budget into Claude Code Pro, so the platform must be free and must not begin billing silently.

Novelty is unavoidable here. Scoping decision §3 records no prior experience with any deployment platform, so whichever option wins costs learning hours. That means novelty cannot be a differentiator between options, only a cost to budget.

## Options considered

| Option       | Weighted score | The detail that decided it                                                                                                                                                                              |
| ------------ | -------------: | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Vercel       |           3.95 | On-demand invalidation of an already-rendered route is first-party rather than adapted, and an account was already provisioned and verified on 2026-09-06 — hours that count directly against C1.       |
| Netlify      |           3.70 | Equal on server-side execution, public access, and git-push deployment. The entire gap is that incremental regeneration arrives through an adapter rather than natively, which is one unverified claim. |
| GitHub Pages |           1.30 | No server runtime at all. FR-AUTH-01 and FR-CONT-01 are unimplementable and FR-SYS-02 is unreachable, which is why it scores zero on two criteria rather than merely low.                               |

Scores are from `docs/tech-evaluation.md` Decision 2 and represent 0.80 of the total weight; the free-tier criterion (0.20) is unscored pending verification.

## Decision

I will deploy to Vercel's free plan, with deployment triggered by push to `main` through the platform's own Git integration rather than by a credentialed deploy step in CI. **I did take the top-scored option, but the margin over Netlify is 0.25 — exactly the coin-flip threshold — and sensitivity analysis narrows it rather than widening it.** The tiebreaker is not in the matrix: the account is already provisioned and verified, which is worth real hours against C1's budget. This decision should therefore be understood as resting on convenience plus one unverified claim about a competitor's regeneration support, not on a decisive technical margin.

## Consequences

**Positive**

- FR-SYS-02 has a first-party invalidation mechanism rather than one supplied by an adapter, which removes a layer that could lag the framework.
- NFR-PERF-01 benefits from edge delivery of pre-rendered pages with no work on my part.
- Git-push deployment means there are no deploy credentials to store anywhere, which removes an entire secret from NFR-SEC-02's surface rather than protecting it.
- Charter §4's "runnable by others" is satisfied by a public URL on a platform subdomain with TLS included, at no cost and with no domain purchase (C3).
- NFR-REL-01's poller can reach the same URL a grader would, so availability is measured against what readers actually get.

**Negative**

- The decision rests on a 0.25 margin and one unverified claim. If verification shows the competing platform's support is equivalent, this ADR is a coin flip resolved by convenience — a weaker justification than the score implies, and it should be read that way.
- The runtime filesystem is read-only. This already cost the original file-based content plan, and it will cost again the moment an essay needs an image: there is no requirement covering media and nowhere to write it (A3 is doing load-bearing work here).
- Serverless invocations share no memory and no disk. This single fact is behind the three highest-risk integration boundaries in the project — the database connection, cache invalidation, and FR-CMT-03's rate limiter. An in-memory rate-limit counter works flawlessly in development and accomplishes nothing in production, silently. Mitigation: hold rate-limit state in the database, and prove both FR-SYS-02 and FR-CMT-03 on the deployed URL. Cost: two spike sessions in Weeks 5–7.
- FR-SYS-02 cannot be meaningfully tested in development, because the local server caches almost nothing and therefore reports a pass regardless. Mitigation: verify from a second device on a different network within sixty seconds of publishing, rather than from the browser that published. Cost: a documented procedure rather than hours.
- No prior experience with this platform (scoping decision §3). Budget: **VERIFY** hours inside the walking-skeleton estimate, which C1 already marks as uncuttable.

## Revisit trigger

- If the free plan begins requiring a payment method without a hard spend cap, or if bandwidth or build-minute limits are reached under A2's assumed readership of tens per day.
- If verification shows the competing platform's regeneration support is equivalent or better **and** a second independent reason to move appears. One reason alone does not justify the migration cost against C1.
- If FR-SYS-02 cannot be demonstrated on the deployed URL by the **end of Week 7**. The platform's highest-weighted criterion would then be failing, and the choice reopens before construction depends on it.

## Verification

| Claim in this ADR                                                                                                           | Source                           | Checked on                |
| --------------------------------------------------------------------------------------------------------------------------- | -------------------------------- | ------------------------- |
| Vercel free plan — deploys, seats, bandwidth, whether a card is required, whether overage bills or throttles                | VERIFY — vendor pricing page     | 2026-09-06, **re-verify** |
| Netlify's current support level for incremental regeneration and any free-plan restriction (**this claim decides the ADR**) | VERIFY — vendor documentation    | VERIFY                    |
| Node.js versions the platform supports, and the version to pin under `engines`                                              | VERIFY — vendor documentation    | VERIFY                    |
| Current name and signature of the on-demand revalidation API                                                                | VERIFY — framework documentation | VERIFY                    |
