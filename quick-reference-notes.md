Book Link: https://cuwcs.com/litman-books/capstone-project/chapters/01

FR-nnn: subject
(Fucntional Requirement)


    Hat          |	  The question it asks                                      |   	It produces	It dominates
Product owner	    Who is this for, and what is it worth?	                        Scope decision, priorities, the demo narrative	Weeks 2, 12, 15

Business analyst	What exactly must it do, in words a stranger can verify?	    Requirements, acceptance criteria, traceability	Weeks 3–4

Architect	        What are the pieces, and why these pieces?	                    Technology evaluation, decision record technical spec Weeks 5–6

Project manager	    Will this fit in the hours we have, and what could kill it?	    Charter, work breakdown, schedule, risk register, the log	Weeks 1, 7

Developer	        How do I make it work?	                                        Working code, commits tied to requirement IDs	Weeks 9–10, 12

Tester	            How do I make it fail?	                                        Test plan, test suite, defect log   Week 11

Technical writer	Can somebody else use this without me?	                        README, runbook, handoff guide, change log	Weeks 13–14




Phase	                    Weeks	    Hours	    Share
Inception	                1–2	        30	        12.5%
Requirements	            3–4	        30	        12.5%
Design & specification	    5–6	        30	        12.5%
Planning	                7	        15	        6.3%
Design review & checkpoint	8	        15	        6.3%
Construction	            9, 10, 12	45	        18.8%
Verification	            11	        15	        6.3%
Documentation	            13	        15	        6.3%
Deployment & transition	    14	        15	        6.3%
Delivery	                15–16	    30	        12.5%
Total		                            240	        100%




Activity	                                Hours	        Share
Writing code	                            84	            35%
Testing, debugging, and fixing	            36	            15%
Requirements, design, and specification	    48	            20%
Planning, tracking, and replanning	        18	            7.5%
Documentation and handoff	                30	            12.5%
Deployment and release	                    12	            5%
Presentation and delivery	                12	            5%

<!--
### FR-IDX-06 — Rank results by relevance
**Priority:** Won't
**Requirement:** The system shall be able to order search results by relevance, placing an essay whose title matches the keyword above an essay whose body matches it, whenever results are returned.
**Rationale:** As the corpus grows, a passing mention in a long body would otherwise outrank the essay actually about the subject, which the Reader would experience as the search being broken.
**Acceptance criteria:**
- Given essay A with the keyword in its title and essay B with it only in its body, when the visitor searches, then A is listed before B.
- Given two essays matching equally, when results render, then they are ordered consistently between identical repeated searches.
**Source:** Own decision — scoping memo feature 3.

### FR-IDX-07 — Show matched excerpts
**Priority:** Won't
**Requirement:** A visitor shall be able to see, for each search result, an excerpt containing the matched keyword with that keyword visually distinguished from the surrounding text.
**Rationale:** A list of bare titles gives the Reader no basis for choosing between results. Seeing the keyword in its context is what makes that choice possible.
**Acceptance criteria:**
- Given a result matching within the body, when results render, then an excerpt surrounding the match is shown with the keyword visually distinguished.
- Given a result matching only in the title, when results render, then an excerpt from the opening of the essay is shown instead.
**Source:** Own decision — scoping memo feature 3.

### FR-IDX-08 — Keep the query in the URL
**Priority:** Won't
**Requirement:** A visitor shall be able to bookmark or share a set of search results, by means of the active query being reflected in the URL query string.
**Rationale:** The Reader who finds a useful result set should be able to return to it later, and the Author wants to be able to link to a search from outside the site.
**Acceptance criteria:**
- Given a search for `determinism`, when results render, then the URL contains that query.
- Given a URL containing a query, when it is opened in a new browser session, then the same results are shown without the keyword being retyped.
- Given the browser back control is used after a search, when the previous page loads, then the prior query state is restored.
**Source:** Own decision.
-->

fr-017-write-functional-requirements