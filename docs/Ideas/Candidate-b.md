# Idea Canvas — Candidate B

**Candidate name:** Pokemon Collection Catalog
**Date started:** 2026-9-3 **Where it came from:** hobby

---

## 1. Problem statement

For Pokemon TCG Collectors who want a way to put their collection of cards in a digital collection where they can view all in the same place. The problem is putting a whole collection in a database takes forever because searching by specific card name takes a long time to find and often returns too many options. This costs hours of searching through cards with similar names. Today they use apps with faulty searches or try spotty AI image detection, which fails because searching by name returns too many similar cards and the AI does not work very easily because of lighting.

## 2. Evidence a user exists

- **Person spoken to:** S.W. (Pokemon TCG Collector)
- **Date and length:** 2026-9-6, 15 minutes long
- **Three verbatim quotes:**
  1. "Whenever I search a card by name, I find so many with the same name, it takes awhile to find my specific card."
  2. "TCG Player does not have good tools for collection formatting, I cannot permanently put my favorite cards at the top of the list."
  3. "It should be better designed to take in the set number by default."
- **The workaround they already use:** "I have to try different search parameters like changing the name or searching with the unique set number, but the set number on that app doesn't always work."
- **Full write-up:** `docs/Ideas/Interviews/interview-sw.md`

## 3. Candidate scope (Must features only)

| #   | Feature (one vertical slice each) | Hours |
| --- | --------------------------------- | ----: |
| 1   | Card Search by Set #              |     8 |
| 2   | Add card to collection folder     |     9 |
| 3   | Share collection                  |     9 |
| 4   | View Card info Page               |    10 |
| 5   | Landing Page with all folders     |    10 |
| 6   | Create new folder                 |     7 |
| 7   | Login Page                        |     7 |
|     | Walking skeleton + CI             |    12 |
|     | Deployment + clean-machine test   |     3 |
|     | **Construction total**            |    75 |

Budget: plan on **60 hours**, hard ceiling **75**. Above 75 you are borrowing from
testing and documentation, which are graded.

## 4. Out of scope — will NOT be built

1. I will not build on a mobile application.
2. I will not add a search for anything but by pokemon name or set number.
3. I will not create a messaging feature.
4. I will not create a friendship system.
5. I will not create an offline feature.
6. I will not create a downloadable application.
7. I will not add a website style changing feature.
8. I will not create an automatic sort function.

## 5. Feasibility screen

| Gate                                             | Verdict | Evidence (dated)                                                                                                                                                                                                                                                                                               |
| ------------------------------------------------ | ------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Build** — novelty load ≤ 2                     | fail    | React (New), Express.js(New)                                                                                                                                                                                                                                                                                   |
| **Get** — every dependency exercised for real    | pass    | PokeWalletAPI: curl command worked 9-6-2026                                                                                                                                                                                                                                                                    |
| **Ship** — a named deployment target, terms read | pass    | MongoDB(New) Free Tier 9-6-2026, Vercel (Node.js)(New) Free Tier 9-6-2026                                                                                                                                                                                                                                      |
| **Show** — a stranger sees it work in 10 minutes | pass    | 1. Navigate to correct URL listed in README 2. Create login information 3. Create folder using button 4. Use search by card set number to find a card 5. Add card to list. 6. View list to see cards. 7. Move cards around to format 8. Create new lists for different tasks 8. Share folder with someone else |

**Technologies:** React (New), Express.js(New), MongoDB(New), Vercel (Node.js)(New)
**Novelty load:** 4 (To clarify, I've used this stack before, so these are all about half new, but to be honest, I marked them new.)

## 6. The one hard part

The one hard part of this project will be sharing the folders/lists of cards in one persons collection to another person. I think this can be achieved by sending access through email, but I will have to figure out how to setup the viewing portion.

## 7. Scorecard (1–5 each; weight in parentheses)

| Criterion                   | (w) | Score | Weighted |
| --------------------------- | --: | ----: | -------: |
| Evidence a user exists      |   3 |     5 |        8 |
| Fits ~45 hours of features  |   3 |     1 |        5 |
| Novelty load                |   2 |     2 |        4 |
| Dependencies verified       |   2 |     4 |        6 |
| Demonstrable in ten minutes |   1 |     5 |        6 |
| **Total (max 55)**          |     |       |       29 |

## 8. If this candidate is rejected

If this project is rejected, it will be from a combination of too many deemed "new" technologies and from having too many features necessary for it to be a worthy application. I think the combination of new technologies and too much scope would kill this project if not handled delicately in time range. I would like to revisit this project when I learn these technologies better to make all of the features easier to create.
