# ADR 0003 — Administrator access control

- **Status:** Accepted
- **Date:** 2026-09-28
- **Decider:** Luke Witte
- **Requirements affected:** FR-AUTH-01 … FR-AUTH-04, FR-CONT-01 … FR-CONT-08, FR-CMT-05, NFR-SEC-01, NFR-SEC-02, NFR-SEC-03
- **Related ADRs:** 0002 (the platform's edge must pass cookie flags through unchanged)

## Context

There is exactly one privileged user of this system and there will never be more. A1 states it as an assumption with a verify-by date of 2026-10-04; C6 states it as a constraint and rules out reader accounts entirely. Requirements §1 says the same thing in prose: no outside login service, only administrator access for the author.

That single fact collapses the problem. A full identity system exists to answer the question "which of my many users is this, and what is that particular user permitted to do?" With one user who is permitted to do everything, the question reduces to a yes or no: does this request carry proof of knowing one secret? A yes-or-no needs no user table, no registration, no password reset flow, and no roles. Recognising that is most of the decision.

What the requirements do need is narrower than an auth library provides. FR-AUTH-01 needs a single enforcement point covering every route under `/admin`, including routes I have not written yet — because a per-page guard is a thing that eventually gets forgotten on one page. FR-AUTH-03 needs a session that survives between short irregular authoring sittings without retyping a passphrase on every navigation. Neither of those requires an identity provider.

C4 caps new technologies at two for the semester, and both slots are already committed by ADR 0001 and ADR 0004. Any auth library is therefore a third thing to learn, against a 60–75 hour budget with the scope-cut trigger already dated to 8 November. That constraint is doing real work in this decision rather than being cited decoratively.

The cost of the obvious answer is that I own the security boundary. NFR-SEC-02 and NFR-SEC-03 exist in the specification _because of_ this decision, not as general good practice.

## Options considered

| Option                                                              | Weighted score | The detail that decided it                                                                                                                                                                                                                              |
| ------------------------------------------------------------------- | -------------: | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Shared passphrase in an environment variable, signed session cookie |           3.85 | Matches the requirement exactly — one secret, no user records — and adds one small signing dependency rather than a framework with concepts C6 has no use for.                                                                                          |
| Auth.js                                                             |           2.40 | Built around identity providers and persisted user records. A credentials-only configuration is possible but works against the library's grain, and brings session-store and adapter concepts that exist to solve a problem this project does not have. |
| Hosted identity provider                                            |           2.20 | A managed user directory is the product being purchased, and C6 says there are no users to manage. It would work; it would also be paying for the one thing explicitly out of scope.                                                                    |

Scores are from `docs/tech-evaluation.md` Decision 3 and represent 0.85 of the total weight; the hosted provider's free-tier criterion (0.15) is unscored. Halving the top criterion's weight still leaves the chosen option ahead by roughly 1.0, so this decision does not rest on a single guessed number.

## Decision

I will authenticate the single administrator by comparing a submitted passphrase against the `ADMIN_PASSWORD` environment variable, and carry the resulting session in an HTTP-only, Secure, signed cookie whose expiry is no more than seven days from issue. Enforcement lives in one middleware file matching every route beginning `/admin`, so routes added later are covered without being individually remembered. No user record exists anywhere in the system, and rotating the environment variable is the password-reset mechanism.

## Consequences

**Positive**

- FR-AUTH-01 through FR-AUTH-03 are satisfied in roughly forty lines, with no schema addition, no registration form, and no reset email path to build or secure.
- The data inventory's credential group holds no user data at all, which is what keeps the privacy position in requirements §6 defensible.
- NFR-SEC-02's surface is small and enumerable: three environment variables, none of which may appear in the repository or the client bundle.
- A single enforcement point means FR-AUTH-01 covers admin routes that do not exist yet, which is the failure mode a per-route guard invites.
- No third-party service sits in the authentication path, so there is no additional dependency to add to the free-tier watch list and nothing that can start billing.

**Negative**

- The security boundary is hand-written and therefore mine. Signing, flag-setting, and redirect validation are all my code, and this option scored **lowest of the three** on exactly that criterion. NFR-SEC-01 through NFR-SEC-03 exist as compensating verification, which means the cost of this choice is paid in test effort rather than avoided.
- One secret, with no revocation granularity. If the passphrase leaks there is nothing to revoke but the secret itself, and A1's "only ever one administrator" stops being incidental and becomes load-bearing. Mitigation: rotating both the passphrase and the signing secret invalidates every outstanding session immediately. Cost: minutes — but only if I notice the leak, which NFR-SEC-02 is the only thing checking.
- FR-AUTH-04's `next` parameter is an open-redirect vector if honoured without validation, turning my own login page into a hop that sends visitors elsewhere. Mitigation: accept only relative paths beginning with `/admin`; FR-AUTH-04's third acceptance criterion tests it. Cost: one condition.
- Cookie flags behave differently between development and production. A `Secure` cookie is not set over plain HTTP, so the sign-in path can silently fail on `localhost` while working correctly when deployed — or the reverse, which is worse. Mitigation: set the flag conditionally on environment. Cost: one line, plus knowing to look for it.
- **Login attempts are not rate-limited by any current requirement**, which leaves the passphrase open to unlimited guessing. Mitigation: reuse FR-CMT-03's per-IP counting pattern on the login route. Cost: small, but it needs a requirement identifier it does not currently have, and it should be added to §5 before this ADR is relied upon.

## Revisit trigger

- If A1 turns false and a second person needs write access. One shared secret cannot distinguish or revoke individual credentials, so this ADR is superseded on the day a second author is **agreed**, not the day they start work.
- If any requirement is added that needs per-reader state — a saved reading position, display preferences, a profile — since C6 would then no longer be a constraint but a choice being actively defended.
- If NFR-SEC-02 ever returns a non-zero count, meaning a secret reached the repository or the client bundle. That forces rotation and a review of whether one shared secret remains sufficient.
- If more than **three failed sign-in attempts per ten minutes** are observed from one address once login rate limiting exists, indicating the passphrase is being guessed at rather than mistyped.

## Verification

| Claim in this ADR                                                                      | Source                                                                             | Checked on |
| -------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------- | ---------- |
| Cookie-signing library — current version and licence                                   | VERIFY — package registry and project `LICENSE`                                    | VERIFY     |
| Whether the middleware runtime provides the crypto API the signing library requires    | VERIFY — framework and platform documentation                                      | VERIFY     |
| Cookie flag behaviour (`HttpOnly`, `Secure`, `SameSite`) across the platform's edge    | VERIFY — platform documentation, then observed on the deployed `Set-Cookie` header | VERIFY     |
| Hosted identity provider free-plan monthly-active-user allowance and billing behaviour | VERIFY — vendor pricing page                                                       | VERIFY     |
