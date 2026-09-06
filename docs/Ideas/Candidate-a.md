Idea Canvas — Candidate A

**Candidate name:** Personal Philosophy Website
**Date started:** 2026-9-1 **Where it came from:** Hobby / Secondary Major

---

## 1. Problem statement

For myself, because I like to tell people about the topics I've written about. The problem is that I would like a way to post my essays and papers somewhere others can read them and I can show them to people easily. This currently costs a missed good conversation. When I cannot pull the paper I was trying to tell someone about, that specific topic of conversation often dies because there is not a next thought. Today I try to pull the paper up in my OneDrive files, which fails because I have papers in many different folders for the classes I wrote them for, additionally, this search requires good signal to access and open, which is very unreliable for OneDrive.

## 2. Evidence a user exists

**User:** Truthfully only myself (Others could, but I cannot plan on that.)
**Competitive Scan:**
JSTOR, Standford Encyclopedia of Philosophy, MindMatters.ai:
These are all typical sources for finding philosophical essays. JSTOR and Stanford are more academic paper databases while MindMatters is more of a news site. My project idea would be to combine these two styles into something cohesive and made for my own essays. I wanted to post the kind of essays that would be in JSTOR and Stanford, not news articles, but I like the style of highlighting an essay on the landing page that is pertinent to todays issues.

## 3. Candidate scope (Must features only)

| #   | Feature (one vertical slice each)                   | Hours |
| --- | --------------------------------------------------- | ----: |
| 1   | Currently reading essay page                        |     8 |
| 2   | Page for viewing all uploaded Essays                |     9 |
| 3   | Available Essays Table prefab for each page         |     9 |
| 4   | Landing page with general info and search bar       |     8 |
| 5   | Function for pulling/Parsing from .md essays folder |     8 |
|     | Walking skeleton + CI                               |    10 |
|     | Deployment + clean-machine test                     |     3 |
|     | **Construction total**                              |    55 |

Budget: plan on **60 hours**, hard ceiling **75**. Above 75 you are borrowing from
testing and documentation, which are graded.

## 4. Out of scope — will NOT be built

1. A public form of posting essays.
2. A commenting feature for readers.
3. A download feature for downloading essays.
4. A settings page.
5. A feature for summarizing an essay automatically.
6. Any form of AI feature.
7. An extensive login process. (just need developer and public sides)
8. A feature for automatic source validation.

## 5. Feasibility screen

| Gate                                             | Verdict     | Evidence (dated)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| ------------------------------------------------ | ----------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **Build** — novelty load ≤ 2                     | pass        | Next.js(New) Installed 9-2-2026                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| **Get** — every dependency exercised for real    | pass / fail | none                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| **Ship** — a named deployment target, terms read | pass        | Vercel Free Tier Account Created 9-2-2026                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| **Show** — a stranger sees it work in 10 minutes | pass / fail | 1: Find Project URL in GitHub README file 2. Paste URL into a search engine 3. View Landing Page 4. Use the navigation tree on the left side or search bar to see different essays 5. Click a link or paste keywords/title to navigate to a selected paper 6. Find paper title and information at the top of each page 7. Read paper by scrolling to the bottom of the page 8. Navigate to new paper through the tree on the left side 9. Use Home Button to return to the landing page 10. Check again later for new essays posted by developer |

**Technologies:** Next.js (new) · Vercel Web Hosting (new)
**Novelty load:** 2

## 6. The one hard part

Writing a function to parse the markdown files, I have done this for JSON, but depending upon how different different, it could be difficult.

## 7. Scorecard (1–5 each; weight in parentheses)

| Criterion                   | (w) | Score | Weighted |
| --------------------------- | --: | ----: | -------: |
| Evidence a user exists      |   3 |     3 |        6 |
| Fits ~45 hours of features  |   3 |     3 |        6 |
| Novelty load                |   2 |     4 |        6 |
| Dependencies verified       |   2 |     5 |        7 |
| Demonstrable in ten minutes |   1 |     5 |        6 |
| **Total (max 55)**          |     |       |       31 |

## 8. If this candidate is rejected

If this project gets rejected, it will be because it was too simple to produce a valuable enough project for my Capstone assignment. I still like the project because it would be a personal, fun way to display my philosophy work and ideas on the internet, but it does not have many different features, and it would be for only personal use. If I reject it for Capstone, I want to revisit the project another time when I have free time and what to just get some practice in Web Development with Next.js.
