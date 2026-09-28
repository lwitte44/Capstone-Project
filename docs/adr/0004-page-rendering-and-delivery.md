# ADR 0004 — Page rendering and delivery strategy

- **Status:** Accepted
- **Date:** 2026-09-28
- **Decider:** Luke Witte
- **Requirements affected:** FR-SYS-02, NFR-PERF-01, NFR-PERF-03, FR-READ-01 … FR-READ-05, FR-HOME-01 … FR-HOME-04, FR-IDX-01 … FR-IDX-04, FR-CONT-01, FR-AUTH-01
- **Related ADRs:** 0002 (the platform must honour the invalidation this strategy requests), 0001 (the store this strategy reads from)

## Context

Two requirements pull in opposite directions, and together they select the strategy before any framework is named. FR-SYS-02 wants content as fresh as possible: sixty seconds from publish to visible. NFR-PERF-01 wants pages as fast as possible: LCP of 2.5 seconds or less on a throttled mobile profile. Freshness argues for rendering as late as possible; speed argues for rendering as early as possible.

Three rendering strategies exist and only one fits through that corridor. **Build-time-only generation** is the fastest possible delivery and fails FR-SYS-02, because content is frozen until the next deploy. **Per-request rendering** is always current and fails NFR-PERF-01, because every reader pays a database round trip and, on serverless infrastructure, occasionally a cold start on top. **Pre-rendering with selective invalidation** satisfies both. Neither requirement alone would have narrowed the field; the pair does.

Charter §3 lists React as a technology already known, and that distinction matters more than it first appears. It separates learning a new component model from learning a new server execution model. Only the second is genuinely novel here, which is what keeps the declared novelty load in the evaluation at 2 rather than 3.

FR-CONT-01 and FR-AUTH-01 need server-side code in the same application as the public pages. Splitting the admin write path into a separate API service would double the deployables, the environment-variable sets, and the things that must be kept in step — against C1's hours and C2's requirement that work be resumable across two sittings.

Finally, FR-READ-02 and FR-READ-03 need markdown rendered to HTML with footnotes surviving as linked references. Citations run throughout the existing essays, so footnote support is not optional polish; it is the difference between a rendered essay and one with stray bracket-caret markers in the middle of the prose.

## Options considered

| Option                             | Weighted score | The detail that decided it                                                                                                                                                                                                              |
| ---------------------------------- | -------------: | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Next.js, App Router                |           4.85 | Pre-renders routes and exposes a first-party call to invalidate one after a write — the only mechanism on the table that satisfies FR-SYS-02 and NFR-PERF-01 simultaneously — and its component model is React, which is already known. |
| Astro                              |           3.90 | Wins the markdown criterion outright with a first-class content pipeline, but on-demand invalidation of an already-built page is its weaker story, and its component model is its own rather than one already learned.                  |
| React SPA with a separate Node API |           2.50 | Client-rendered pages fetch essay text after load, which makes FR-SYS-02 trivial and loses NFR-PERF-01 outright — plus two deployables to keep in step against C1.                                                                      |

Scores are from `docs/tech-evaluation.md` Decision 4 and represent the full 1.00 of weight. Halving the top criterion still leaves the chosen option ahead by roughly 0.7, so the winner is stable under sensitivity analysis.

## Decision

I will build the site as a Next.js application using the App Router, with public pages pre-rendered and invalidated on demand from the same server-side action that writes an essay. Server-side code for FR-CONT-01 and FR-AUTH-01 lives in that same application and deploys as one unit. Markdown is rendered server-side through the remark and rehype pipeline with footnote support enabled, so FR-READ-03's references resolve to a linked list at the foot of the essay.

## Consequences

**Positive**

- FR-SYS-02 and NFR-PERF-01 are satisfiable at the same time, which no other option on the table achieves.
- Existing React knowledge transfers, so the novelty is the server execution model rather than an entire framework. This is the single largest reason the novelty load stays inside C4's cap.
- One deployable, one host, one CI pipeline, one set of environment variables — which is what C1's hours can actually carry.
- FR-READ-03's footnotes and FR-READ-04's heading anchors are both ecosystem plugins rather than code I write and test myself.
- NFR-PERF-03's page-weight ceiling is easier to hold, because pre-rendered pages ship HTML rather than a client bundle that must fetch and render content.

**Negative**

- The server/client component split is the dominant learning cost and it is invisible in every requirement. Code that works as a server component breaks the moment it acquires a click handler, and the error messages are not always self-explanatory. This is where the hours actually go. Budget: **VERIFY** hours inside the walking-skeleton estimate.
- Invalidation correctness is entirely on me. FR-SYS-02 names three paths — landing page, index, and the affected essay — and invalidating only the essay is the natural mistake, because the essay is the page you check. The unpublish direction (published back to draft) is easier still to forget; FR-SYS-02's third acceptance criterion exists to force that test.
- FR-SYS-02 cannot be verified in development at all. The local server caches almost nothing, so every local test is a false pass that tells me nothing about production. Mitigation: verify from a second device on a different network, rather than from the browser that published. Cost: a documented procedure rather than hours.
- The footnote syntax in my source markdown must match the dialect the renderer parses. A mismatch renders `[^1]` as four literal characters mid-sentence with no error raised anywhere. Mitigation: render one real, heavily footnoted essay end to end before relying on the pipeline. Cost: one short session.
- **Astro is a better fit for the markdown half of this project and I am not taking it.** That is a real loss, not a rhetorical concession — its content pipeline is closer to what a corpus of essays wants. I am trading it for the invalidation mechanism FR-SYS-02 demands.
- The framework's rendering model is the piece of this stack most likely to shift under me over a semester. Mitigation: pin exact versions in a committed `package-lock.json` and take no major upgrade during weeks 9–12. Cost: not receiving fixes either, and a pinned version that is stale by Week 16.

## Revisit trigger

- If FR-SYS-02 cannot be demonstrated on the deployed URL by the **end of Week 7**. The entire justification for this framework is that one mechanism; if it does not work, Astro plus a short time-based revalidation interval becomes the better trade and this ADR is superseded.
- If FR-SYS-02 is relaxed to "visible on next deploy," which would make a static generator sufficient and remove both the database and this framework from the critical path — reopening ADR 0001 and ADR 0002 at the same time.
- If a major framework version changes the rendering or invalidation API in a way that breaks the build during weeks 9–12, since C1 has no hours allocated to a migration in that window.

## Verification

| Claim in this ADR                                                                                    | Source                                        | Checked on |
| ---------------------------------------------------------------------------------------------------- | --------------------------------------------- | ---------- |
| Next.js current stable version, and the current name and signature of the on-demand revalidation API | VERIFY — framework documentation              | VERIFY     |
| Astro's current on-demand invalidation capability (the claim that decided this ADR against it)       | VERIFY — framework documentation              | VERIFY     |
| Markdown footnote plugin — package name, current version, and the syntax dialect it parses           | VERIFY — package documentation                | VERIFY     |
| Node.js version required by the framework and supported by the platform                              | VERIFY — framework and platform documentation | VERIFY     |
| Next.js and React SPDX licence identifiers, read from each project's `LICENSE` file                  | VERIFY — project repositories                 | VERIFY     |
