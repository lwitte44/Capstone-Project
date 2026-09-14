# Software Requirements Specification — <Your Project Name>

<!--
COPY THIS FILE into your repository as docs/requirements.md and delete every
comment block as you fill it in. Keep the section numbering; the Week-16 rubric
and the Week-4 traceability matrix both key off it.
Requirement IDs are FR-<AREA>-<nn>. Assign an ID once and never reuse it.
Retire an ID by marking it Withdrawn; do not renumber.
-->

**Author:** Luke Witte **Version:** 1.0 **Date:** 9-13-2026
**Status:** `Draft` | In review | Baselined

---

## 1. Purpose and Scope

<!--
One paragraph: what this system is for, who it serves, and what problem it removes.
One paragraph: what is explicitly _outside_ the boundary of this release.
-->

**Purpose:** This website is for displaying my philosophy essays in one place, available all the time while I have browser access, and for allowing others to read my essays. This website primarily serves myself, but others can use the link and read my essays as well. It removes the problem of having to search through my file system to find essays when I want to send them to someone.
**Scope:** This website is made to be run by one person, myself. I will not have an outside login service, only an admin password access for the author. It will not have an outside service to monitor comment responses, that is on the author. It will not have a real-time comment feature, the comments are sent, but only shown to the public when the author approves them. This website will not have a public essay uploading feature, all essays are uploaded only by the author.

## 2. Stakeholders and Personas

| Persona           | Who they are   | What they need from the system | Evidence they exist                       |
| ----------------- | -------------- | ------------------------------ | ----------------------------------------- |
| Reader | This would be anyone, including the author, who goes to the site to read an essay. | They need to be able to reach the website from a link, view available-to-read essays, and then pick one to read. | When there are essays published online, there are always people to read them. Examples: JSTOR, Stanford Encyclopedia of Philosophy, and MindMatters.ai |
| Author | The creater of the website and primary user | The ability to quickly pull up and reference an essay they wrote. | Project Purpose Statement |
| Maintainer | The one stuck with making sure the website stays active and comepletely functional | They need to be able to have documentation for the code, and quick access to editing the essays on the website. | The course’s own handoff test |


<!-- Include the maintainer who inherits this repository. They are a stakeholder. -->

## 3. Definitions
 <!-- >
Define every term your requirements use in a project-specific sense. If a reader
could interpret a word two ways, it belongs here.
-->
| Term | Definition in this document |
| ---- | --------------------------- |
| Administrator | used as the same as the Author |
| Author | used as the same as Administrator |
| Reader | public user of the website |
| Corpus | complete list of all essays |
| Essay | one published writing |
| Body | the main bulk of text for an essay |
| Tag | the keywords inputed by author that describe an essay |
| Featured | the landing page displayed essay as designated by the author |
| Publish | add the essay to be viewed on the website |
| Permalink | a particular essays public URL |
| Comment | a messaged submitted by a Reader to be posted on an essay page |
| Accepted Comment | the website took into a queue a public comment |
| Approved Comment | the comment has been looked at and approved for public display on website |
| Public Route | any URL not beginning with /admin, used by public traffic |
| Admin Route | any URL  beginning with /admin, used by the Author to post essays and monitor comments |
| Session | state of being authenticated as admin while carrying a cookie |
| Passphrase | password used by Author to get into /admin |



## 4. Assumptions and Dependencies

- **Assumption:** <something you are taking as true without proof> — _If false:_ <consequence>
- **Dependency:** <an external service, dataset, device, or person you rely on> — _If unavailable:_ <fallback>

- **Assumption:** There will only ever be one Author/Administrator. If false: There is no way to distinguish or revoke credentials because there is only one admin password.
- **Assumption:** There will only be readership in the 10s per day. If false: The free tiers of databases and for Vercel will kick in and FR-SYS-02 will need to be revisited.
- **Assumption:** The essays entered are text only with no pictures, diagrams, or media. If false: The current method of display with markdown will not work anymore.
- **Assumption:** The learning curve for the technologies used in this Capstone is managable. If false: the commenting feature will be cut first.
- **Assumption:** Converting the existing Word documents to Markdown preserves italics, headings, and footnotes without manual repair. If False: Each essay needs re-marking by hand before it can be pasted, at roughly thirty minutes per essay, which is unbudgeted.
- **Assumption:** Commenting amount stays managable for one person to take care of approving. If false: Approving comments will be come a chore to deal with

- **Dependency:** Vercel Free Tier for providing hosting of the website URL at no cost. If unavailable: I will either need to find a new hosting service, or I may have to pay for a better tier.
- **Dependency:** Pandoc command for converting Word docs to markdown. If unavailable: I will have to manually input footnotes and fix things like italics.
- **Dependency:** Npm and Next.js for coding and running the website. If unavailable: The website will be completely broken and unusable.
- **Dependency:** Turso for a database to store essays, comments, and tags. If unavailable: I will not be able to store data and the website will break.

## 5. Functional Requirements

<!-- Repeat this block for every requirement. Group by area. 

### FR-<AREA>-<nn> — <short imperative name>

**Priority:** Must | Should | Could | Won't (this release)
**Requirement:** <Actor> shall be able to <action> <object> <under what condition>.
**Rationale:** Why this exists, and which persona asked for it.
**Acceptance criteria:**

- Given <starting state>, when <the actor does this>, then <this observable thing is true>.
- Given <edge or failure case>, when <trigger>, then <defined behavior>.
//Every Must and Should requirement carries at least two acceptance criteria in Given/When/Then form: one happy path, one thing going wrong.

**Source:** <interview, observation, regulation, your own decision — name it>

Examples: 

FR-DETECT-01 (Must) — The tool shall report every one-minute interval in which
the count of HTTP 5xx responses exceeds three standard deviations above the
mean 5xx count for the preceding sixty minutes.
NFR-PERF-02 (Should) — The tool shall process a 1 GB log file in under 120
seconds on the reference machine named in the technical specification.

FR-REC-02 (Should) — Given at least five items in the household pantry, the
system shall return between one and three recipe suggestions within eight
seconds, each listing the pantry items it uses and any ingredients not on hand.
Acceptance: against the fixed 20-case evaluation set committed to the repository,
at least 16 cases return 1-3 suggestions within 8 s, each using >= 3 on-hand
items, with zero suggestions presenting a missing ingredient as on hand.

-->

### FR-AUTH-01 — Guard admin routes
**Priority:** Should
**Requirement:** The system shall be able to block any request to a route beginning `/admin` when that request does not carry a valid administrator session.
**Rationale:** The Author is the only person permitted to create or alter site content. 
**Acceptance criteria:**
- Given a valid administrator session cookie, when a request is made to any `/admin` route, then the requested page is rendered.
- Given no administrator session cookie is present, when a request is made to `/admin`, then a redirect to the login page is returned and no admin content is served.
**Source:** Own decision — security practice; enforcement point chosen so that admin routes added after this requirement was written are covered automatically.

### FR-AUTH-02 — Authenticate with a shared passphrase
**Priority:** Should
**Requirement:** An administrator shall be able to establish an authenticated session by submitting a passphrase that matches the value held in the `ADMIN_PASSWORD` environment variable.
**Rationale:** The site has exactly one privileged user, so a full account system — registration, password reset, stored user records — would add cost and create extra work outside the scope of this Capstone.
**Acceptance criteria:**
- Given the correct passphrase, when the Author submits the login form, then a session is established and the Author is taken to the admin dashboard.
- Given an incorrect passphrase, when the form is submitted, then no session is established and an error is shown that does not indicate whether any part of the input was correct.
**Source:** Own decision — replaces the need for a full login process, but also allows for admin manipulation of essays.

### FR-AUTH-03 — Persist the session in a signed cookie
**Priority:** Should
**Requirement:** The system shall be able to carry an administrator session in an HTTP-only signed cookie that expires no later than seven days after issue, following a successful sign-in.
**Rationale:** The Author publishes in short irregular sittings and should not retype a passphrase on every page. The signature prevents a hand-crafted cookie from granting access, and the HTTP-only flag prevents page scripts from reading it.
**Acceptance criteria:**
- Given a successful login, when the response is returned, then it sets a cookie marked HttpOnly and Secure with an expiry no more than seven days ahead.
- Given a cookie whose payload has been altered, when it is presented, then signature verification fails and the session is rejected.
- Given a cookie past its expiry, when it is presented, then the session is rejected and the Author is redirected to the login page.
**Source:** Own decision — security practice; allows for continued author access while still enforcing security.

### FR-AUTH-04 — Return to the requested page after sign-in
**Priority:** Could
**Requirement:** An administrator shall be able to arrive at the originally requested admin route after completing sign-in, whenever the request that triggered the login was for a specific admin page.
**Rationale:** The Author commonly follows a bookmark straight to an editor page. Landing on the dashboard instead forces a second navigation to reach the page already asked for.
**Acceptance criteria:**
- Given no session, when the Author requests `/admin/essays/12/edit` and then signs in successfully, then the Author lands on `/admin/essays/12/edit`.
- Given no session and a direct request to `/admin/login`, when sign-in succeeds, then the Author lands on the admin dashboard.
- Given a next value that is not a relative path beginning with `/admin`, when sign-in succeeds, then the value is discarded and the Author lands on the admin dashboard.
**Source:** Own decision — usability and URL security.

---

## Content management (CONT)

### FR-CONT-01 — Create an essay

**Priority:** Must
**Requirement:** An administrator shall be able to create an essay by supplying a title, a Markdown body, a publication date, and zero or more tags.
**Rationale:** This is the core authoring action for the Author persona. It allows for creating a new essay that the website can then read and display
**Acceptance criteria:**
- Given a completed form with title, body, and date, when the Author saves, then a new essay record exists and appears in the admin essay list.
- Given a form with no tags entered, when the Author saves, then the essay is created with an empty tag set.
- Given tags that do not yet exist, when the Author saves, then those tags are created and associated with the essay.
**Source:** Own decision — derived from scoping-memo feature 5, revised after the Vercel read-only filesystem constraint was identified.

### FR-CONT-02 — Edit an existing essay
**Priority:** Should
**Requirement:** An administrator shall be able to edit every field of any existing essay, whether that essay is in draft or published state.
**Rationale:** Essays are revised after publication — typos, clarifications, corrected citations — and the Author has no other route to change live content once it is in the database.
**Acceptance criteria:**
- Given a published essay, when the Author changes its body and saves, then the public reading page subsequently shows the revised text.
- Given an essay that does not exist, when its edit URL is requested, then a not-found response is returned.
**Source:** Own decision — scoping-memo feature 5.

### FR-CONT-03 — Reject incomplete essays
**Priority:** Must
**Requirement:** The system shall be able to reject a save whose title or body is empty and report which field is missing, whenever an administrator submits the essay form.
**Rationale:** An essay with no body would publish a blank page at a permanent URL. Catching the omission at the form is cheaper than discovering it after a link has been shared.
**Acceptance criteria:**
- Given an empty title or body, when the Author saves, then the save is refused and a message naming the title or body field is displayed.
- Given a refused save, when the error is shown, then the Author's other entered values remain present in the form.
**Source:** Own decision — input validation practice.

### FR-CONT-04 — Derive a slug from the title
**Priority:** Should
**Requirement:** The system shall be able to derive a URL slug from an essay's title by lowercasing it and replacing each run of non-alphanumeric characters with a single hyphen, whenever an essay is first created.
**Rationale:** Slugs are what Readers see and share. Deriving them removes a field the Author would otherwise complete by hand and keeps their form consistent across the corpus.
**Acceptance criteria:**
- Given the title "On Free Will & Determinism", when the essay is created, then the slug is `on-free-will-determinism`.
- Given a title with leading or trailing punctuation, when the essay is created, then the slug carries no leading or trailing hyphen.
- Given a title containing consecutive spaces or punctuation, when the essay is created, then the slug contains no repeated hyphens.
**Source:** Own decision. This differentiates URLs to specific essays.

### FR-CONT-05 — Reject duplicate slugs
**Priority:** Should
**Requirement:** The system shall be able to reject an essay whose derived slug matches the slug of an existing essay, at the point of saving.
**Rationale:** Two essays at one URL would make one of them unreachable. The database also enforces uniqueness on this column, so an unhandled collision would surface to the Author as a crash rather than a message.
**Acceptance criteria:**
- Given an existing essay with slug `on-truth`, when the Author saves a new essay producing the same slug, then the save is refused with a message naming the conflict.
- Given a refused save, when the Author alters the title so the derived slug is unique, then the save succeeds.
**Source:** Own decision — follows from the unique constraint in the data model.

### FR-CONT-06 — Freeze slugs after publication
**Priority:** Could
**Requirement:** The system shall be able to keep an essay's slug unchanged after that essay's first publication, regardless of any later edit to its title.
**Rationale:** A shared or cited link must not break. The Reader persona commonly arrives from a link held elsewhere, and the Author cannot recall a link once it has been sent.
**Acceptance criteria:**
- Given a published essay at `/essays/on-truth`, when the Author changes its title and saves, then the essay remains reachable at `/essays/on-truth`.
- Given a draft essay, when the Author changes its title and saves, then the slug is regenerated from the new title.
**Source:** Own decision — permalink stability, essays that make it to publication should keep titles.

### FR-CONT-07 — Hold essays in draft or published state
**Priority:** Could
**Requirement:** The system shall be able to hold each essay in exactly one of two states, draft or published, and exclude every draft from all public routes.
**Rationale:** The Author works on an essay across several sittings and needs it stored but unreadable in the meantime. The original file-based plan had no way to express "written but not ready".
**Acceptance criteria:**
- Given an essay in draft state, when a visitor requests its permalink, then a not-found response is returned.
- Given an essay in draft state, when a visitor views the landing page, index, or search results, then that essay does not appear.
- Given the Author changes an essay from draft to published, when a visitor requests its permalink, then the essay is served.
**Source:** Own decision - could have WIP essays in the database.

### FR-CONT-08 — Designate a featured essay
**Priority:** Should
**Requirement:** An administrator shall be able to mark one published essay as featured, clearing the flag from whichever essay previously held it.
**Rationale:** The landing page is built around a single featured essay. Permitting two would leave that page's behavior undefined, and requiring the Author to unset the old one manually invites the error.
**Acceptance criteria:**
- Given essay A is currently featured, when the Author marks essay B as featured, then B is featured and A is not.
- Given a draft essay, when the Author attempts to mark it featured, then the action is refused.
**Source:** Own decision — scoping memo feature 4.

---

## Essay reading page (READ)

### FR-READ-01 — Serve essays at stable permalinks
**Priority:** Must
**Requirement:** A visitor shall be able to reach any published essay at the permalink `/essays/{slug}`, without signing in.
**Rationale:** This is the page the entire site exists to deliver, and its URL is what gets shared. The Reader persona typically arrives here first rather than at the landing page.
**Acceptance criteria:**
- Given a published essay with slug `on-truth`, when a visitor requests `/essays/on-truth`, then the essay is rendered.
- Given no administrator session, when a visitor requests any published essay, then the essay is rendered without a login prompt.
**Source:** Own decision — scoping memo feature 1.

### FR-READ-02 — Render Markdown as formatted HTML

**Priority:** Must
**Requirement:** The system shall be able to render an essay's Markdown body as HTML supporting headings, bold, italic, ordered and unordered lists, blockquotes, links, inline code, and fenced code blocks, whenever an essay page is generated.
**Rationale:** The Author writes in Word and converts to Markdown before pasting. Italics for titles and technical terms, and blockquotes for quoted passages, appear constantly in the existing essays and must survive to the rendered page.
**Acceptance criteria:**
- Given a body containing `*emphasis*`, when the page renders, then that word appears in italics rather than surrounded by literal asterisks.
- Given a body containing a fenced code block, when the page renders, then its content appears in a monospaced block with internal formatting preserved.
- Given a body containing a Markdown link, when the page renders, then a working anchor element is produced.
**Source:** Own decision — scoping memo feature 5.

### FR-READ-03 — Render footnotes
**Priority:** Should
**Requirement:** The system shall be able to render Markdown footnote syntax as numbered in-text references linked to a footnote list at the end of the essay, whenever an essay page is generated.
**Rationale:** Citations are carried in footnotes throughout the Author's existing corpus. Without this, references would render as literal `[^1]` markers scattered through the body text.
**Acceptance criteria:**
- Given a body containing a footnote reference and its definition, when the page renders, then a numbered reference appears in place and links to the matching entry at the foot of the essay.
- Given a footnote reference with no matching definition, when the page renders, then the page still renders and the orphaned reference does not disrupt the surrounding text.
**Source:** Own decision — derived from inspection of the existing essay feature needs.

### FR-READ-04 — Anchor section headings
**Priority:** Could
**Requirement:** A visitor shall be able to link directly to any level-2 or level-3 sub-heading within an essay, by means of a unique id and a copyable anchor link generated for each such heading.
**Rationale:** The Reader persona cites and discusses specific sections of an argument. Without anchors, the finest granularity available to them is the essay as a whole.
**Acceptance criteria:**
- Given an essay with two level-2 headings, when the page renders, then each carries a distinct id and a visible anchor link.
- Given two headings with identical text, when the page renders, then their generated ids are still distinct from one another.
- Given a visitor opens a URL ending in a heading id, when the page loads, then the view is positioned at that heading.
**Source:** Own decision - helpful for more specific citation link work.

### FR-READ-05 — Display essay metadata
**Priority:** Must
**Requirement:** A visitor shall be able to see an essay's title, publication date, and tags above the body text, on every essay page.
**Rationale:** The Reader arriving from a shared link has no surrounding context. Date and tags establish when the piece was written and what it concerns before they commit to reading it.
**Acceptance criteria:**
- Given a published essay carrying two tags, when a visitor opens it, then the title, formatted publication date, and both tags appear above the body text.
- Given an essay with no tags, when a visitor opens it, then the title and date appear and no empty tag region is shown.
**Source:** Own decision — scoping memo feature 1.

---

## Landing page (HOME)

### FR-HOME-01 — Present the featured essay
**Priority:** Must
**Requirement:** A visitor shall be able to see the featured essay's title, publication date, and an excerpt on the landing page, without any interaction.
**Rationale:** The landing page's job is to start the Reader reading rather than to make them choose. A single featured piece carries the Author's editorial recommendation.
**Acceptance criteria:**
- Given an essay flagged as featured, when a visitor opens the landing page, then that essay's title, date, and excerpt appear and the title links to its permalink.
- Given the featured essay has no stored excerpt, when the landing page renders, then an excerpt derived from the opening of the body is shown in its place.
**Source:** Own decision — scoping memo feature 4.

### FR-HOME-02 — Fall back when nothing is featured
**Priority:** Must
**Requirement:** The system shall be able to display the most recently published essay in the featured position, whenever no essay carries the featured flag.
**Rationale:** An empty main screen region is the worst first impression the site can offer, and this state occurs on every fresh deployment before the Author has flagged anything.
**Acceptance criteria:**
- Given no essay is flagged featured and three essays are published, when a visitor opens the landing page, then the most recently published essay occupies the featured position.
- Given no essays are published at all, when a visitor opens the landing page, then an explanatory message appears in place of the featured essay rather than an empty region.
**Source:** Own decision — empty-state handling.

### FR-HOME-03 — List essays in a sidebar by year
**Priority:** Should
**Requirement:** A visitor shall be able to see a sidebar listing every published essay grouped by publication year, most recent year first, on the landing page.
**Rationale:** The Reader who has finished one essay needs a route into the rest of the corpus. Grouping by year gives the list structure without obliging the Author to maintain a category scheme.
**Acceptance criteria:**
- Given published essays spanning two calendar years, when a visitor opens the landing page, then the essays appear under two year headings with the later year first.
- Given a draft essay, when the landing page renders, then it does not appear anywhere in the sidebar.
**Source:** Own decision — scoping memo feature 4, "file tree" element.

### FR-HOME-04 — Collapse the sidebar on narrow screens
**Priority:** Should
**Requirement:** A visitor shall be able to view the landing page with the sidebar collapsed behind a toggle control, whenever the viewport is narrower than 768 pixels.
**Rationale:** Most Readers arrive on a phone from a shared link. A fixed sidebar at that width leaves no usable room for the featured essay it sits beside.
**Acceptance criteria:**
- Given a viewport narrower than 768 pixels, when a visitor opens the landing page, then the sidebar is hidden and a toggle control is visible.
- Given the sidebar is collapsed, when the visitor activates the toggle, then the full essay list becomes visible.
- Given a viewport of 768 pixels or wider, when the page loads, then the sidebar is shown expanded and no toggle is required.
**Source:** Own decision — mobile readership improvement.

---

## Browsing, filtering, search (IDX)

### FR-IDX-01 — List all published essays
**Priority:** Must
**Requirement:** A visitor shall be able to see all published essays in reverse chronological order with title, publication date, and excerpt, on the essay index page.
**Rationale:** The Reader who wants to survey the whole corpus needs one place that shows everything, which the featured-essay landing page deliberately does not.
**Acceptance criteria:**
- Given five published essays, when a visitor opens the index, then all five appear newest first with title, date, and excerpt.
- Given a draft essay, when the index renders, then it does not appear.
- Given no published essays at all, when a visitor opens the index, then an explanatory empty state is shown rather than a blank list.
**Source:** Own decision — scoping memo feature 2.

### FR-IDX-02 — Filter the index by tag
**Priority:** Should
**Requirement:** A visitor shall be able to restrict the index to essays carrying a given tag, by requesting `/essays?tag={tag}`.
**Rationale:** The Reader interested in one subject should not have to scroll the whole corpus. Tags are the Author's existing means of grouping essays by topic.
**Acceptance criteria:**
- Given three essays tagged `ethics` and two without it, when a visitor requests `/essays?tag=ethics`, then exactly the three tagged essays are listed.
- Given a tag that no published essay carries, when it is requested, then the empty state defined in FR-IDX-04 is shown.
- Given a filtered index, when the page renders, then the active tag is identified on the page.
**Source:** Own decision — scoping memo feature 2.

### FR-IDX-03 — Display the tag set
**Priority:** Should
**Requirement:** A visitor shall be able to see every tag currently in use, each linking to its filtered index, on the essay index page.
**Rationale:** Filtering is unusable if the Reader cannot discover which tags exist, and the Author maintains no published list of them anywhere else.
**Acceptance criteria:**
- Given published essays carrying three distinct tags, when a visitor opens the index, then all three tags appear, each linking to its filtered view.
- Given a tag attached only to draft essays, when the index renders, then that tag is not listed.
**Source:** Own decision - needed for showing the Reader persona the browsing options.

### FR-IDX-04 — Explain an empty result set
**Priority:** Should
**Requirement:** The system shall be able to display an explanatory message with a link to the unfiltered index, whenever a tag filter or search matches no essays.
**Rationale:** A blank list reads as a broken page. The Reader needs confirmation that the filter ran and found nothing, together with a route back to the full corpus.
**Acceptance criteria:**
- Given a tag matching no published essays, when the filtered index renders, then a message explains that nothing matched and offers a link to all essays.
- Given a search query matching no essays, when results render, then the same treatment is applied.
**Source:** Own decision — empty-state handling.

### FR-IDX-05 — Match a keyword against essay content
**Priority:** Could
**Requirement:** A visitor shall be able to find essays by supplying a keyword that is matched against essay titles, body text, and tags.
**Rationale:** The landing page design places a search field in the left column. The Reader looking for a half-remembered essay needs to search its text, not only its title.
**Acceptance criteria:**
- Given a keyword appearing only in one essay's body, when the visitor searches for it, then that essay appears in the results.
- Given a keyword appearing only as a tag, when the visitor searches for it, then the essays carrying that tag appear.
- Given a draft essay containing the keyword, when the visitor searches, then it does not appear in the results.
**Source:** Own decision — scoping memo feature 3.

---

## Comments and moderation (CMT)

### FR-CMT-01 — Submit a comment without an account
**Priority:** Could
**Requirement:** A visitor shall be able to submit a comment on a published essay consisting of a display name and body text, without creating an account or signing in.
**Rationale:** The Author wants reader response but has explicitly ruled out operating a user-account system. Requiring registration would also suppress nearly all replies from the Reader persona.
**Acceptance criteria:**
- Given a published essay, when a visitor submits a display name and comment body, then the submission is accepted and a confirmation is shown.
- Given an empty display name or body, when submission is attempted, then it is refused with a message naming the missing field.
**Source:** Own decision — added during scoping review as the one data type that cannot be held in version-controlled files.

### FR-CMT-02 — Enforce comment length limits
**Priority:** Could
**Requirement:** The system shall be able to reject a comment whose display name exceeds 60 characters or whose body exceeds 2,000 characters, reporting which limit was exceeded, at the point of submission.
**Rationale:** Unbounded input is both a storage and a page-layout problem, and a length ceiling is the cheapest first filter against automated submissions.
**Acceptance criteria:**
- Given a display name of 61 characters, when the comment is submitted, then it is refused and the message names the display-name limit.
- Given a body of 2,001 characters, when the comment is submitted, then it is refused and the message names the body limit.
- Given input at exactly 60 and 2,000 characters, when submitted, then the comment is accepted.
**Source:** Own decision — input validation practice.

### FR-CMT-03 — Rate-limit comment submissions
**Priority:** Could
**Requirement:** The system shall be able to reject more than three comment submissions originating from the same IP address within any ten-minute window.
**Rationale:** A public comment form with no account requirement is found by automated spam quickly. Rate limiting is the only defence that does not depend on the Author being present to intervene.
**Acceptance criteria:**
- Given three comments already submitted from one address within ten minutes, when a fourth is attempted, then it is refused with a message asking the visitor to wait.
- Given eleven minutes have elapsed since the first of those submissions, when a further comment is attempted, then it is accepted.
- Given any submission is recorded, when the record is written, then the address is stored only as a hash and never in plain form.
**Source:** Own decision — abuse mitigation; hashing chosen so the system does not accumulate a log of who read what.

### FR-CMT-04 — Quarantine comments pending approval
**Priority:** Could
**Requirement:** The system shall be able to record every accepted comment in a pending state and exclude pending comments from all public output, until an administrator approves them.
**Rationale:** Nothing written by a stranger should reach the Author's readers unreviewed. This is what makes an account-free comment form defensible at all.
**Acceptance criteria:**
- Given a newly submitted comment, when the essay page is viewed by any visitor, then that comment does not appear.
- Given a newly submitted comment, when the Author opens the moderation queue, then it appears there.
- Given a pending comment, when the page's cached version is regenerated, then the comment still does not appear publicly.
**Source:** Own decision — moderation requirement.

### FR-CMT-05 — Moderate the pending queue
**Priority:** Could
**Requirement:** An administrator shall be able to approve or delete each pending comment, from a queue ordered oldest first.
**Rationale:** The Author needs one place to clear moderation rather than visiting each essay in turn, and oldest-first ordering ensures no submission waits indefinitely behind newer ones.
**Acceptance criteria:**
- Given three pending comments, when the Author opens the queue, then all three are listed oldest first, each showing the essay it belongs to.
- Given a pending comment, when the Author approves it, then it leaves the queue and becomes publicly visible on its essay.
- Given a pending comment, when the Author deletes it, then it leaves the queue and never becomes publicly visible.
**Source:** Own decision - allows for author to see comments first and filter out any with bad names.

### FR-CMT-06 — Display approved comment
**Priority:** Could 
**Requirement:** A visitor shall be able to read approved comments beneath the essay body, in ascending order of submission time, each showing the commenter's display name and date.
**Rationale:** Comments are only worth collecting if Readers can see them, and chronological order is what makes a sequence of replies legible as a conversation.
**Acceptance criteria:**
- Given two approved comments, when a visitor opens the essay, then both appear beneath the body, earliest first, each with display name and date.
- Given an essay with no approved comments, when a visitor opens it, then the comment area shows an invitation to comment rather than an empty region.
**Source:** Own decision.

### FR-CMT-07 — Neutralise markup in comments
**Priority:** Could
**Requirement:** The system shall be able to escape HTML within comment text so that any submitted markup is displayed as literal characters, whenever a comment is rendered.
**Rationale:** Comment text is untrusted input written by strangers. Rendering it as markup would allow a commenter to inject scripts or arbitrary content into the Author's pages and onto other Readers' screens.
**Acceptance criteria:**
- Given a comment body containing a `<script>` tag, when the comment is displayed, then the tag appears as visible text and no script executes.
- Given a comment body using angle brackets as ordinary punctuation, when it is displayed, then those characters appear exactly as typed.
**Source:** Own decision — security practice.

---

## Site-wide behavior (SYS)

### FR-SYS-01 — Handle unknown essay URLs
**Priority:** Must
**Requirement:** The system shall be able to return a 404 status and a not-found page, whenever a request is made to `/essays/{slug}` that does not match a published essay.
**Rationale:** Mistyped and stale links are routine, and a draft essay must be indistinguishable from one that never existed. An unhandled error here would be the Reader's likeliest first impression of a broken site.
**Acceptance criteria:**
- Given no essay with slug `nonexistent`, when a visitor requests `/essays/nonexistent`, then a 404 status and a not-found page are returned.
- Given an essay held in draft state, when a visitor requests its permalink, then the same 404 response is returned.
- Given the not-found page renders, when the visitor reads it, then it offers a link back to the essay index.
**Source:** Own decision — error handling.

### FR-SYS-02 — Refresh cached pages after publication
**Priority:** Must
**Requirement:** The system shall be able to refresh the cached landing page, essay index, and affected essay page within sixty seconds of an administrator publishing or editing an essay.
**Rationale:** Pages are generated ahead of time for performance, which means a publish would otherwise not become visible until the next deployment. The Author expects to publish and then see the result.
**Acceptance criteria:**
- Given a newly published essay, when sixty seconds have elapsed, then it appears in the landing page sidebar and on the index without a redeployment.
- Given an edit to a published essay's body, when sixty seconds have elapsed, then the essay page serves the revised text.
- Given an essay moved from published back to draft, when sixty seconds have elapsed, then it no longer appears on any public page.
**Source:** Own decision — consequence of the static generation strategy chosen for performance.


## 6. Non-Functional Requirements

Placeholder for Week 4. Do not write vague quality words here now; write nothing
and fill it in when you can make each one measurable.

## 7. Out of Scope (the Won't-Have List)

Things a reasonable reader might expect and will not get in this release, each
with one line of reasoning. A short list here means you have not thought hard enough.

| Not building | Why not | Revisit when |
| ------------ | ------- | ------------ |

## 8. Open Questions

| #   | Question | Who can answer it | Needed by |
| --- | -------- | ----------------- | --------- |

## 9. Document Change Log

| Date         | Version | Change                | Reason      |
| ------------ | ------- | --------------------- | ----------- |
| <YYYY-MM-DD> | 1.0     | Initial specification | Milestone 3 |
