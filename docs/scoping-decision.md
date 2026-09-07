# Scoping Decision — Personal Essays Website

**Author:** Luke Witte · **Date:** 2026-9-6 · **Course week:** 2

---

## 1. Problem

This project is personal for myself, because I like to tell people about the topics in papers I've written about. The problem is that I would like a way to post my essays and papers somewhere others can read them and I can show them to people easily, but I am obviously not experienced enough to get published mainstream. This currently costs a missed good conversation. Conversations are valuable because they are chief ways we exchange information, learn new things, and test our own viewpoints. When I cannot pull up the paper I was trying to tell someone about, that specific topic of conversation often dies because there is not a next thought. Today I try to pull the paper up in my OneDrive files, which fails because I have papers in many different folders for the classes I wrote them for, additionally, this search requires good signal to access and open, which is very unreliable for OneDrive on mobile.

## 2. Evidence a user exists

**User:** Mostly myself, but I hope to have it public.
**Competitive Scan:**
JSTOR, Standford Encyclopedia of Philosophy, MindMatters.ai:
These are all typical sources for finding philosophical essays. JSTOR and Stanford are more academic paper databases while MindMatters is more of a news site. My project idea would be to combine these two styles into something cohesive and made for my own essays. I wanted to post my essays into the database while also having a thread on the homescreen featuring an essay important to current issues.

## 3. Chosen scope — Must features

| #   | Feature (one vertical slice each)                     |                                                                                                                                               Hours |
| --- | ----------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------: |
| 1   | Currently Open Essay Page                             |                                                                                                           8, this feature has slack, but is a must. |
| 2   | Page for viewing all uploaded Essays                  |                                                                         9, this feature has slack, but can be cut if not enough time after week 10. |
| 3   | Search Essay by Keyword                               |                                                                         9, this feature has slack, but can be cut if not enough time after week 10. |
| 4   | Landing page with Featured Essay and File Tree (Left) |                                                                     10, This page is a must, but may be made easier if other must features are cut. |
| 5   | Function for pulling/Parsing from .md essays folder   |                                                                          8, this one may not have slack, it is the bread and butter of the project. |
| 6   | Function for getting philosopher information from API |                          8, this is the first feature to go, its hours do have extra built in though, it shouldnt be bad to parse the json returned |
|     | Walking skeleton + CI                                 |                                                                            12, I have never done this before so 12 is my best estimate, cannot cut. |
|     | Deployment + clean-machine test                       |                                                                    3, this should leave slack, but is unknown with never having done Vercel before. |
|     | **Construction total**                                | 59, currently on the low end, but I think this leaves room for adaption if everything goes easier than expected, or extra work room if it goes bad. |

Plan: 60 hours. Hard ceiling: 75. My number: <N>. <One sentence saying whether that
leaves slack, and what happens if it does not.>

## 4. Should features — built only if there is room

**Ranked Most Likely to Build 1-... (Will drop lowest on list first)**
| #| Should Feature |Cost| Week Planned for |
|--|------------------------|----|-----------------:|
|1 | Search by any words. | 6 | Week 12 |
|2 | Summarize Essay | 8 | Week 12 |
|3 | Commenting on an essay | 12 | Week 12(Late) |
|4 | Login Page | 6 | Week 13 |

## 5. Out of scope — will not be built

"A public feature for posting essays" · "A settings page" · "A feature for automatic source validation" · "Any new post notifications" · "Any export feature" · "Any feature to find similar essays somewhere on another site." · "Any feature to allow user to sort essays into folders" · "A mobile application" · "A feature to estimate time to read new essay" · "An AI chatbot for talking about the essay currently being read"

## 6. Accepted tradeoffs

<Any place you deliberately chose a cheaper design that costs the user something.
Name the cost. Name why you accepted it. Name what would make you revisit.>

I chose to parse the locally stored .md files instead of having to upload new essays to a database each time I wanted to add one. This costs be the long term ease of access and time I will spend to manually add a new file with each new essay and pushing to GitHub manually. I accepted this because for the scope of Capstone, I can finish this faster than setting up a database and I will save time for other features. I will revisit this sometime after Capstone is done when I want a more professionally done personal website.

## 7. Rejected candidates

**Rejected: Pokemon Collection Catalog.** It failed the novelty gate because it had too many new, moving parts that I would have to learn how to use. This would have been too much for the Capstone scope. This project is up for revisitation when I have a larger scope time.

**Rejected: WW2 Galaga Game.** It failed at having potential users and in the must features. While I know there would be users, it could be a small fanbase of specific people, it also does not yet have enough features and should be thought out more at a later date. I am open to revisiting when the project does not have as many requirements and I want to just build a game for fun.

## 8. Hour budget, reconciled

| Weeks | Phase                       |   Hours |
| ----- | --------------------------- | ------: |
| 1–2   | Inception                   |      30 |
| 3–4   | Requirements                |      30 |
| 5–6   | Design                      |      30 |
| 7     | Planning                    |      15 |
| 8     | Design review + midterm     |      15 |
| 9–12  | Construction + verification |      60 |
| 13    | Documentation               |      15 |
| 14    | Deployment + handoff        |      15 |
| 15–16 | Presentation + delivery     |      30 |
|       | **Total**                   | **240** |

<One sentence: does your construction total fit inside the 60/75 line, and what did
you cut to make it fit?>

My project does fit inside the 60/75 line. I made it fit by having simple features and dropping the idea of a database/upload function for now so that I can focus on having good essay formatting and readability across different screens.

## 9. The one hard part

<Name exactly one. Two sentences on what makes it hard. This is what you will talk
about for ten minutes in Week 16.>

The hardest part will be writing a function to parse the markdown files, I have done this for JSON, but depending upon how different different, it could be difficult. I assume that I will be able to search through using the headings, but I will have to see if that is how simple it is.

## 10. Risks and the scope-cut trigger

| Risk                                                                            | Likelihood                                                                            | What it costs me                                                                  | Early warning sign                                                                                                 |
| ------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------ |
| I am also working on my Philosophy Senior Seminar at the same time as Capstone. | Certain                                                                               | It takes extra time out of every week that could be spent on this project alone.  | I get to week 8 and I don't have much done in Senior Seminar. It would mean I need to put less time into Capstone. |
| I have broken weeks in weeks 7, 12, 14                                          | Certain to have other events, uncertain how they will effect work hours until closer. | Work hours during the week that need to be spent on Capstone, especially Week 12. | I will get through the first half of each of those weeks with not enough done before the weekend.                  |

**Scope-cut trigger.** If I am not 100% done with all previous milestones by Sunday,November 8th (day before week 12), I will cut the philosophers information feature and then the essay search bar, all else is a must have or no website. Decided now, in advance, so I do not have to decide it while panicking.

## Dependency-Verification Table (For All Candidates)

| Dependency      | Candidate | Exercised                                                                         | Result                                    | Key?                                     | Rate Limit                                                                                     | Terms Read |
| --------------- | --------- | --------------------------------------------------------------------------------- | ----------------------------------------- | ---------------------------------------- | ---------------------------------------------------------------------------------------------- | ---------: |
| PokeWalletAPI   | B         | curl GET search Charizard                                                         | 200, returned every card named charizard  | Yes, key aquired from site in 15 minutes | 100/hr 1000/day                                                                                |   9-6-2026 |
| MongoDB         | B         | used before, can open a new cluster                                               | created a new test cluster named Capstone | No, need account, I have one             | M0 Free Tier, 512 MB of storage                                                                |   9-6-2026 |
| Vercel          | A,B       | I made a free tier account                                                        | it worked, I added my GitHub Repo         | no                                       | Free Deploy, 1 dev on team only                                                                |   9-6-2026 |
| PhilosophersAPI | A         | Get request URL https://philosophersapi.com/api/philosophers/name/Marcus+Aurelius | returned JSON correctly                   | no                                       | none listed, website has almost no information except what the api returns or how to prompt it |   9-6-2026 |

## Cut-Order

1. Philosophers Information from API
2. The search Bar
3. The available essays file tree.

I will know I need to begin to cut from these features if I reach milestone 8 and I do not have a fleshed out plan for how I will build the whole website and if I do not have all requirements from previous milestones done.

## Bottom-Up Hour Estimate

| #   | Feature (one vertical slice each)                     | Hours |
| --- | ----------------------------------------------------- | ----: |
| 1   | Currently Open Essay Page                             |     8 |
| 2   | Page for viewing all uploaded Essays                  |     9 |
| 3   | Search Essay by Keyword                               |     9 |
| 4   | Landing page with Featured Essay and File Tree (Left) |    10 |
| 5   | Function for pulling/Parsing from .md essays folder   |     8 |
| 6   | Function for getting philosopher information from API |     8 |
|     | Walking skeleton + CI                                 |    12 |
|     | Deployment + clean-machine test                       |     3 |
|     | **Construction total**                                |    67 |

**240-Hour Scope Sizer Says...:**

- INPUT: 5 features, 1 external integration, simple data complexity, 2 new technologies, and deployed where stranger can see it
- Estimated Range: 129-267 hrs
- Most Likely: 198 hrs
- Weeklu Load, 16 Weeks: 12.4 h/wk
- Most hours go into implementation (83)

---

**Signed:** Luke Witte, 2026-9-6
**AI use for this document:** None
