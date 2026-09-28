# ADR 0005 — Licensing of code and essay text

- **Status:** Accepted
- **Date:** 2026-09-28
- **Decider:** Luke Witte
- **Requirements affected:** NFR-MAINT-01, NFR-SEC-05, Charter 4 (definition of finished), `requirements.md` 2 Maintainer persona and 13
- **Related ADRs:** 0001, 0002, 0003, 0004 — each carries a dependency whose borrowed licence must be read, and this ADR depends on that check coming back clean

## Context

The repository holds two kinds of work with genuinely different interests. The **code** is a small publishing system whose value to anyone else is as something to clone, run, and learn from — which is exactly what Charter §4's definition of finished and NFR-MAINT-01's clean-machine test both require a stranger to be able to do. The **essay text** is original philosophical writing whose value to me is authorial, and which a stranger has no need to copy in order to read the site.

Doing nothing is not a neutral option. With no licence file, copyright reserves everything by default: nobody may copy, modify, or redistribute any part of the repository. That includes the Maintainer persona in `requirements.md` §2, who inherits this repository under the course's handoff test. A clean-machine test that a person has no legal permission to perform is not a passing test, whatever the stopwatch says.

A single permissive licence across the whole repository would solve the code problem and create an essay problem. MIT permits commercial redistribution provided a copyright notice is retained, which for a body of philosophy essays is not an outcome I want. A single copyleft licence solves neither cleanly: it is written for software and reads oddly over prose, and it would still permit the essays to be redistributed.

So the shape of the problem is one repository containing two bodies of work, while the standard licences each assume a repository contains one kind of thing. The operative constraint is legibility: whatever I do has to be understandable by a stranger in under a minute, because NFR-MAINT-01 gives them thirty minutes in total and none of it should be spent deciphering permissions.

## Options considered

| Option                                           | Weighted score | The detail that decided it                                                                                                                                                                                                              |
| ------------------------------------------------ | -------------: | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| MIT for the code, essay text expressly excluded  |            n/a | The maintainer gets unrestricted permission over exactly the thing they need and none over the thing they do not. Both halves fit in two sentences a reader absorbs at a glance.                                                        |
| MIT across the whole repository, essays included |            n/a | Would permit anyone to republish the essays commercially with only a copyright notice retained. That specific outcome is the one I am declining.                                                                                        |
| GPL-3.0-only for the code, essay text excluded   |            n/a | Would oblige anyone distributing a modified version to open their source. Nobody is going to build a product on a personal essay site, so the obligation buys nothing and costs the maintainer a decision they should not have to make. |
| No licence file at all                           |            n/a | Reserves all rights by default, leaving the Maintainer persona with no permission to clone, run, or extend. Fails Charter §4 and makes NFR-MAINT-01 legally hollow even when it passes technically.                                     |

No weighted matrix exists for this decision and none is offered. The criteria here are legal and authorial rather than technical trade-offs, and a matrix with invented weights over four options would be exactly the rigged artifact Chapter 5 warns about. The deciding detail per option is recorded instead.

## Decision

I will license the source code of this repository under **MIT** (SPDX: `MIT`), with the verbatim MIT text and nothing else in `LICENSE` at the repository root. The **essay text is expressly excluded from that grant and remains all rights reserved** — the essays themselves, wherever they sit: in this repository, in seed data, or in the deployed database.

The exclusion lives in a separate file, `LICENSE-ESSAYS.md`, rather than being appended beneath the MIT text. Automated licence detection on the repository host and in dependency-scanning tools reads `LICENSE` and reports what it finds there; a modified MIT file gets reported as "other", which loses the one machine-readable statement in this arrangement. `README.md` states both halves in its first two lines so a reader meets the scope before the setup instructions.

**This refines `requirements.md` 13**, which currently says the exclusion is stated beneath the MIT text in `LICENSE`. That section needs updating to match.

## Consequences

**Positive**

- The Maintainer persona gets unambiguous permission to clone, run, and extend the code, which is what Charter 4 and NFR-MAINT-01 actually require of the handoff.
- `LICENSE` stays verbatim MIT, so the repository host and any dependency scanner report `MIT` correctly rather than falling back to an unrecognised licence.
- The essays keep their authorial protection without my having to reason about which Creative Commons variant fits, and without a non-commercial clause leaking into the code's terms.
- The whole arrangement is one sentence to explain at the Week 16 handoff, which is the only budget NFR-MAINT-01 leaves for it.

**Negative**

- A two-licence repository is unusual, and a hurried reader will assume MIT covers everything. Mitigation: the scope statement goes in the **first two lines** of `README.md`, not the bottom. Cost: nothing now, but it has to survive every later README edit, and nothing enforces that.
- All rights reserved on the essays means someone wanting to quote at length has no stated permission and must ask me. That is deliberate friction and worth naming rather than pretending the choice is free. If I later decide I want the essays quotable without being asked, that is a Creative Commons licence scoped to the essay text and a **superseding ADR**, not an edit to this one.
- MIT places no obligation on anyone building on the code, so a derivative may stay closed. I accept that because nobody is going to productise a personal essay site — but it is precisely the trade GPL would have avoided, and I am making it knowingly.
- **The exclusion has no machine-readable form.** Tooling will report this repository as MIT and will not know the essays are carved out. There is no fix available at this scale; the statement is for humans, and I should not expect a scanner to honour it.
- This decision is only as sound as the borrowed-licence check in `tech-evaluation.md` 6, **which is not yet done**. If an `AGPL-3.0` or source-available non-OSI dependency enters the production tree, my ability to license my own code under MIT is constrained regardless of what this ADR says. That check is the live risk here, not the choice between MIT and GPL.

## Revisit trigger

- If a second person contributes **essay text**, since "my own work" stops being accurate and the all-rights-reserved statement would need a joint-authorship basis. Same trigger as assumption A1 in ADR 0003.
- If `AGPL-3.0` or a source-available non-OSI licence (for example `SSPL`) appears anywhere in the resolved production dependency tree, as checked in the 6 pass alongside NFR-SEC-05's `npm audit` run. Network copyleft reaches a publicly deployed web application.
- If I decide the essays should be quotable without being asked, replacing all rights reserved with a Creative Commons licence scoped to the essay text.
- If the repository comes to hold a **third** kind of work with a third licence — fonts, icons, images, or a dataset — since two files would become three and the README summary would stop being one sentence.

## Verification

| Claim in this ADR                                                                                         | Source                                                | Checked on     |
| --------------------------------------------------------------------------------------------------------- | ----------------------------------------------------- | -------------- |
| Canonical MIT identifier and licence text                                                                 | <https://spdx.org/licenses/MIT.html>                  | 2026-09-28     |
| SPDX identifier registry, for the dependency identifiers in 6                                             | <https://spdx.org/licenses/>                          | 2026-09-28     |
| Plain-language summary of MIT's obligations, to check the "retain the notice" claim                       | <https://choosealicense.com/licenses/mit/>            | 2026-09-28     |
| How the repository host detects a licence from the `LICENSE` file, and what it reports for a modified one | <https://docs.github.com/en/repositories>             | 2026-09-28     |
| SPDX identifier of every production dependency, read from its own licence file                            | Each project's repository, per `tech-evaluation.md` 6 | Values to read |
