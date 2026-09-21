# Software Requirements Specification — Personal Essay Website

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

| Persona    | Who they are                                                                       | What they need from the system                                                                                   | Evidence they exist                                                                                                                                    |
| ---------- | ---------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Reader     | This would be anyone, including the author, who goes to the site to read an essay. | They need to be able to reach the website from a link, view available-to-read essays, and then pick one to read. | When there are essays published online, there are always people to read them. Examples: JSTOR, Stanford Encyclopedia of Philosophy, and MindMatters.ai |
| Author     | The creater of the website and primary user                                        | The ability to quickly pull up and reference an essay they wrote.                                                | Project Purpose Statement                                                                                                                              |
| Maintainer | The one stuck with making sure the website stays active and comepletely functional | They need to be able to have documentation for the code, and quick access to editing the essays on the website.  | The course’s own handoff test                                                                                                                          |

<!-- Include the maintainer who inherits this repository. They are a stakeholder. -->

## 3. Definitions

 <!-- >
Define every term your requirements use in a project-specific sense. If a reader
could interpret a word two ways, it belongs here.
-->

| Term             | Definition in this document                                                           |
| ---------------- | ------------------------------------------------------------------------------------- |
| Administrator    | used as the same as the Author                                                        |
| Author           | used as the same as Administrator                                                     |
| Reader           | public user of the website                                                            |
| Corpus           | complete list of all essays                                                           |
| Essay            | one published writing                                                                 |
| Body             | the main bulk of text for an essay                                                    |
| Tag              | the keywords inputed by author that describe an essay                                 |
| Featured         | the landing page displayed essay as designated by the author                          |
| Publish          | add the essay to be viewed on the website                                             |
| Permalink        | a particular essays public URL                                                        |
| Comment          | a messaged submitted by a Reader to be posted on an essay page                        |
| Accepted Comment | the website took into a queue a public comment                                        |
| Approved Comment | the comment has been looked at and approved for public display on website             |
| Public Route     | any URL not beginning with /admin, used by public traffic                             |
| Admin Route      | any URL beginning with /admin, used by the Author to post essays and monitor comments |
| Session          | state of being authenticated as admin while carrying a cookie                         |
| Passphrase       | password used by Author to get into /admin                                            |

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

<!--
Placeholder for Week 4. Do not write vague quality words here now; write nothing
and fill it in when you can make each one measurable.

IDs follow `NFR-<AREA>-<nn>`

Each requirement has four fields:
- **Metric** — the number I am measuring.
- **Threshold** — the number it has to hit to pass.
- **Condition** — when a measurement counts. A test run at the wrong time, on the wrong machine, or against the wrong URL does not count.
- **Method** — the exact steps I follow to get the number, using tools I already have.
-->

I have 13 Non-Functional Requirements because I made the decision to have 13 that are necessary for covering all bases.

I do not have a Privacy section because with there being no public login portal, there is no collection of data on users beyond their comments that they enter information into. The website only collects what they write in themselves and every entry is monitored by the admin.

## Performance (PERF)

### NFR-PERF-01 — Essay page loads fast enough

**Priority:** Must
**Metric:** How long an essay page takes to display its main body text (LCP).
**Threshold:** 2.5 seconds or less, using the second-slowest of five runs.
**Condition:** Run against the live Vercel URL, not localhost. Lighthouse set to Mobile, which turns on slow-phone and slow-network throttling. The test essay must be at least 3,000 words, so I am measuring a real essay and not a stub. Cache disabled. All performance tests use my desktop (Windows 11, 32 GB).
**Method:**

1. Open the essay on the live site in Chrome.
2. Press F12, click the Lighthouse tab.
3. Set Mode to Navigation, Device to Mobile, and check Performance only.
4. Click Analyze page load. Write down the LCP figure.
5. Repeat four more times, for five results total.
6. Sort the five and take the second-slowest. That number must be 2.5 s or less.
7. Save all five reports as JSON in `docs/measurements/perf/`.

### NFR-PERF-02 — Saving an essay doesn't hang

**Priority:** Should
**Metric:** How long the save request takes, from clicking Save to the response finishing.
**Threshold:** 1,500 ms or less, on all five attempts. One slow attempt is a fail.
**Condition:** Live site, live Turso database, home internet. I am already logged in, so login time isn't counted. Same 20,000-character test essay every run.
**Method:**

1. Log into `/admin` and open the new-essay form.
2. Press F12, go to the Network tab.
3. Paste in the 20,000-character test body and click Save.
4. Find the POST request in the list and read its Duration column.
5. Repeat five times. Every run must be under 1,500 ms.
6. Export the HAR to `docs/measurements/perf/`.

## Reliability (REL)

### NFR-REL-01 — The site stays up

**Priority:** Must
**Metric:** Percentage of manual checks that get an HTTP 200 back from the landing page, the essay index, and one fixed essay URL.
**Threshold:** 99% or better over any seven-day stretch, and no single outage longer than 30 minutes.
**Condition:** Checked every day at least 3 times against the live site for the four weeks before the Week-14 handoff. Outages I cause by deploying don't count against me, but I have to have logged that deploy in `docs/hours-log.csv` with a timestamp that covers it.
**Method:**

1. Have the step fail if any response isn't 200, so a failure shows up as a red run in the Actions tab.
2. At Week 14, check if there were any outages logged.
3. Record the tally in `docs/measurements/reliability/`.

### NFR-REL-02 — I can recover if the database dies

**Priority:** Should
**Metric:** How many hours of my own work it takes to get a fully working site with all published essays back, starting from an empty database.
**Threshold:** 4 hours or less, with no essay text lost for good.
**Condition:** Practiced on the development database only, never production. The only things I'm allowed to use are my Word originals and the seed script in the repo. I follow the written runbook exactly — if I have to invent a step, that's a bug in the runbook, not a pass.
**Method:**

1. Drop every table in the development database using `turso db shell`.
2. Start a stopwatch.
3. Work through `docs/runbook-restore.md` from the top, without improvising.
4. Log every step and how long it took.
5. Stop the clock when the site works and every essay is back.
6. Write the total time, the number of essays recovered, and every place the runbook was wrong into `docs/measurements/reliability/restore-drill.md`.
7. Fix the runbook. Run this once, in Week 13.

## Security (SEC)

### NFR-SEC-01 — Nothing under /admin works without logging in

**Priority:** Must
**Not allowed:** Serving admin pages, admin data, or draft essay text to anyone who isn't logged in — and accepting a form submission to an admin endpoint from anyone who isn't logged in.
**Metric:** Number of admin routes that return something other than a redirect to login or a 401/403, when requested with no session.
**Threshold:** Zero.
**Condition:** Tested against the live site with all cookies cleared. Must cover every route listed in `docs/routes.md`, and must test form endpoints with POST, not just pages with GET.
**Method:**

1. Keep a list of every admin route in `docs/routes.md`, updated whenever I add one.
2. Write `tests/security/admin-guard.spec.ts` in Playwright with an agent that loops that list.
3. For each route, assert the status is a redirect to login or a 4xx, and that the page body contains none of the admin text markers.
4. Add the test to CI so it runs on every push and fails the build if any route leaks.

### NFR-SEC-02 — No passwords or keys in the repo or the browser

**Priority:** Must
**Not allowed:** Any secret appearing in code the browser downloads, or in any commit — including a commit I later reverted. Git keeps history, so deleting the line afterwards does not fix it.
**Metric:** Number of times the admin passphrase, the session signing secret, or the database connection string appear in (a) the full git history and (b) the built client JavaScript.
**Threshold:** Zero in both.
**Condition:** Checked across the whole history of `main` from the first commit to the handoff tag, and against the build output in `.next/static/`.
**Method:**

1. In PowerShell, run `git log -p | Select-String -Pattern "ADMIN_PASSWORD|SESSION_SECRET|libsql://"`.
2. Run `npm run build`, then `Select-String -Path ".next/static/**/*.js" -Pattern "<the real secret values>"`.
3. Add `gitleaks detect --no-git=false` as a CI step so this runs on every push instead of once.
4. If anything is found, **change the secret**, don't just delete the line. Once a secret is in history, assume it's public.

### NFR-SEC-03 — A malicious comment can't run code

**Priority:** Must
**Not allowed:** Rendering any part of a visitor's comment as live code. Also not allowed: relying on the moderation queue to catch this. Even a comment I mistakenly approve must be harmless.
**Metric:** Number of attack payloads, out of twelve, that manage to run a script, inject into the page, or pop a browser dialog.
**Threshold:** Zero out of twelve.
**Condition:** Tested on the live site with the twelve payloads of malicious attempts to insert code. Every payload gets submitted, approved through admin, and then viewed as a normal reader.
**Method:**

1. For each payload: submit it, approve it via the admin route, load the essay page.
2. Assert that no dialog appeared, that no new `<script>` element exists in the page, and that the payload shows up as visible text.
3. Add to CI.

## Accessibility (ACC)

### NFR-ACC-01 — No serious accessibility errors

**Priority:** Must
**Metric:** Number of axe-core violations rated "serious" or "critical."
**Threshold:** Zero on the landing page, the index, and an essay page.
**Condition:** Scanned on the live site at both 1440 px (desktop) and 390 px (phone) width, and with the sidebar both open and collapsed — FR-HOME-04 creates a second state that one scan would miss.
**Method:**

1. Open the page in Chrome at the target width.
2. F12 → Lighthouse → check Accessibility only → Analyze.
3. Repeat for each page, each width, and each sidebar state.
4. For the detailed list of what's wrong, install the free axe DevTools extension and run it too.
5. Save reports to `docs/measurements/acc/`.

### NFR-ACC-02 — Everything works without a mouse

**Priority:** Should
**Metric:** How many of the eight steps in my keyboard script I can complete using only the keyboard, with the focus outline visible the whole time.
**Threshold:** All eight, with focus always visible and no place where Tab gets stuck.
**Condition:** Done on the live site in both Chrome and Firefox with the mouse physically unplugged, at 1440 px. The eight steps: land on home, reach the sidebar, open an essay, reach a footnote link, come back from the footnote, reach the index, apply a tag filter, reach the comment form.
**Method:**

1. Write the eight steps into `docs/acc-keyboard-script.md`.
2. Unplug the mouse.
3. Walk the script using only Tab, Shift+Tab, Enter, and Space.
4. At each step, screenshot the focus outline as proof it was visible.
5. Mark each step pass or fail in `docs/measurements/acc/keyboard-<date>.md`.
6. Do this once before the Week-14 handoff.

## Usability (USE)

### NFR-USE-01 — A stranger can find an essay unaided

**Priority:** Should
**Metric:** How long it takes someone who has never seen the site to get from the front page to an essay's text on screen.
**Threshold:** 60 seconds or less for at least four of five people, and nobody needs help to finish.
**Condition:** Participants have never seen the site and get one instruction only: "find and open an essay about this "topic"." They use their own phone or laptop, so I'm testing whatever screen they actually have.
**Method:**

1. Recruit five people — classmates or family is fine.
2. Give them the one-line task and start a stopwatch.
3. Say nothing while they work. Write down the time, every wrong turn, and anything they say out loud.
4. Summarize all five sessions in `docs/measurements/usability/first-use-<date>.md`.

### NFR-USE-02 — Error messages say what went wrong and what to do

**Priority:** Should
**Metric:** How many of my error messages name both the problem and the fix.
**Threshold:** All eight in `docs/error-catalogue.md`: empty title, empty body, duplicate slug, comment name too long, comment body too long, rate-limit refusal, wrong passphrase, and the 404 page.
**Condition:** Each one triggered on purpose on the live site and read as if I'd never seen the site. One exception: the wrong-passphrase message must **not** name the cause, because FR-AUTH-02 forbids revealing what part of the input was wrong. It still has to state the fix.
**Method:**

1. Trigger each of the eight conditions deliberately.
2. Copy the exact message text into `docs/error-catalogue.md`.
3. Score each one twice: does it name the cause, and does it say what to do?
4. Rewrite anything that fails either half, then trigger it again to confirm.

## Maintainability (MAINT)

### NFR-MAINT-01 — Someone else can get it running in half an hour

**Priority:** Must
**Metric:** How long it takes a person who has never seen this project to run it locally, using only the README.
**Threshold:** 30 minutes or less, with no questions asked of me and no steps they had to guess.
**Condition:** Either a classmate on their own machine, or me on the laptop after deleting the repo and clearing the npm cache. I am not allowed to help during the run. If they ask a question, the README failed — not the tester.
**Method:**

1. Hand over the repo URL and nothing else. Start a stopwatch.
2. Log every command they run and what happened.
3. Any step they had to figure out on their own gets added to the README.
4. Start over from the top with the corrected README.
5. Record it in `docs/measurements/maintainability/clean-machine-<date>.md`.
6. Do this at Week 13, then again at Week 15 against the final README.

## Portability (PORT)

### NFR-PORT-01 — Works in every browser I claim to support

**Priority:** Must
**Metric:** Number of failing steps in my browser test script, per browser, plus whether the page scrolls sideways on a phone.
**Threshold:** Zero failures in current Chrome, Firefox, and Edge on Windows 11, and current Safari on iOS. No sideways scrolling at 390 px in any of them.
**Condition:** Tested on the live site, on the two most recent versions of each desktop browser, and on a real iPhone for Safari — iOS Safari can't be faithfully faked from Windows.
**Method:**

1. Write the core reading journey into `docs/browser-test-script.md`.
2. Walk it in each browser, see if everything is usable and correctly sized.
3. Save everything to `docs/measurements/portability/`.

## Data Inventory

The data this system touches, grouped into the elements that behave the same way. "Indefinite" means no retention limit is stated in this specification. Highlighted words are questions that must be answered before the end of week 12.

| Data element                                                                  | Why it is needed                                                                     | Where it lives                                                                         | Retention                                                                  | How it is removed                                                                                                                      |
| ----------------------------------------------------------------------------- | ------------------------------------------------------------------------------------ | -------------------------------------------------------------------------------------- | -------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------- |
| Essay content, title, Markdown body, publication date, tags                   | FR-CONT-01 creates it; FR-READ-02 and FR-READ-05 render it; FR-IDX-02 filters by tag | Turso database                                                                         | Indefinite                                                                 | Author edits it under FR-CONT-02. **No requirement deletes an essay.**                                                                 |
| Essay slug                                                                    | FR-CONT-04 derives it; FR-READ-01 serves the essay at it                             | Turso database                                                                         | Indefinite, frozen after first publication by FR-CONT-06                   | Not removable on its own, FR-CONT-06 exists so shared links keep working                                                               |
| Essay state, draft/published, featured                                        | FR-CONT-07 hides drafts; FR-CONT-08 drives the landing page                          | Turso database                                                                         | Indefinite; the featured flag clears when another essay is featured        | Author toggles it in the admin pages                                                                                                   |
| Comment content, display name, body, submission time, pending/approved status | FR-CMT-01 accepts it; FR-CMT-04 quarantines it; FR-CMT-06 displays it                | Turso database                                                                         | Indefinite                                                                 | Author deletes it while pending under FR-CMT-05. **Once approved, no deletion route exists**, and C6 forbids the commenter having one. |
| Hashed IP address                                                             | FR-CMT-03 rate limiting only. Stored as a hash, never in plain form                  | Turso database                                                                         | Rate-limit window is ten minutes, but **no purge afterwards is specified** | No route specified; goes only when its comment does                                                                                    |
| Admin session cookie                                                          | FR-AUTH-03 keeps the Author signed in between sittings                               | The Author's browser, no session record is stored server-side                          | Seven days maximum, per FR-AUTH-03                                         | Clear browser cookies, or wait for expiry                                                                                              |
| Secrets, admin passphrase, session signing secret, database connection string | FR-AUTH-02 and FR-AUTH-03 need the first two; Dependency D4 needs the third          | Vercel environment variables. Never in the repository or client bundle, per NFR-SEC-02 | Until rotated                                                              | Author changes the value in the Vercel or Turso dashboard. This is also the password-reset mechanism, since no user record exists.     |
| Word document originals                                                       | Dependency D2 converts them; NFR-REL-02 restores from them                           | The Author's own OneDrive                                                              | Author's discretion                                                        | Author deletes them locally; the website cannot reach them                                                                             |
| Project records, measurement evidence, test scripts, usability session notes  | Required outputs of the NFRs in 6                                                    | Git repository                                                                         | Life of the repository                                                     | Author deletes and commits. Note the usability notes under NFR-USE-01 describe five real participants.                                 |

## 7. Out of Scope (the Won't-Have List)

Things a reasonable reader might expect and will not get in this release, each
with one line of reasoning. A short list here means you have not thought hard enough.

| Not building               | Why not                                                                                                  | Revisit when                                                   |
| -------------------------- | -------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------- |
| AI Summarization Feature   | It would cost more time than I have for my project.                                                      | I will revisit this when the project scope is larger.          |
| Downloading Feature        | It is not necessary for my personaly essay, additionally, it would increase the time cost of the project | I will revisit if I decide I want people to download my essays |
| A Login Page               | The Reader does not have any functions that they need a login for, it saves scope space.                 | There are new features that need a login page.                 |
| Advertisements             | If people are wanting to read my essays, I do not want to make them deal with ads                        | If I had a more of a viewership.                               |
| A Notification Feature     | I will not add essays often enough to make the cost worth it.                                            | If I got more viewership and had more essays.                  |
| A Subscription Requirement | My essays are not professional enough to make readers pay.                                               | If I got more viewership and had more essays.                  |
| Search by Topic            | I think that using just keyword will suffice for this scope                                              | This is the most likely to add when I have more time.          |

## 8. Open Questions

| #   | Question                                    | Who can answer it                     | Needed by |
| --- | ------------------------------------------- | ------------------------------------- | --------- |
| 1   | How will I store the environment variables? | Dr. Kellogg's APIs class, or research | Week 7    |

## 9. Document Change Log

| Date      | Version | Change                | Reason      |
| --------- | ------- | --------------------- | ----------- |
| 2026-9-13 | 1.0     | Initial specification | Milestone 3 |

## 10. Constraints

| #   | Constraint                                                                                                                                       | Source                                     | What it rules out                                                                                                                                                                  |
| --- | ------------------------------------------------------------------------------------------------------------------------------------------------ | ------------------------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| C1  | 240 total hours for the semester, of which 60 fall in weeks 9–12 for construction and verification.                                              | Charter §3; scoping decision §8            | Any feature not already on the Must list. No new vertical slice may be added after Week 9. No rebuild of anything already working, however unsatisfying it looks.                  |
| C2  | Weeks 7, 8, 12, and 14 are partly consumed by other commitments.                                                                                 | Charter §3, R2; scoping decision §10       | Scheduling the walking skeleton, the deployment, or the clean-machine handoff test inside those weeks. Any task that cannot be stopped and resumed across two separate sittings.   |
| C3  | $20 per month, spent entirely on Claude Code Pro.                                                                                                | Charter §3                                 | Every paid tier: paid hosting, paid database storage, paid uptime monitoring, a purchased domain name, and any commercial testing tool. Every dependency must work on a free tier. |
| C4  | At most two technologies learned from scratch this semester.                                                                                     | Charter §3                                 | Adding a CSS framework, a component library, an ORM, or an auth library that would count as a third new thing to learn on top of Next.js and Turso.                                |
| C5  | Concurrent hard deadlines: Philosophy Senior Seminar across the semester, Apologetics paper due 11/23, Machine Learning project due in December. | Charter §3, R1                             | Any plan whose slack sits in November or December. Catch-up time cannot be assumed to exist in weeks 12–16, because those weeks are already committed elsewhere.                   |
| C6  | One administrator, authenticated by a single shared passphrase, with no public login.                                                            | Requirements §1 Scope; scoping decision §6 | Reader accounts, personalization, saved reading positions, per-reader preferences, a second author, and any ability for a commenter to edit or delete their own comment.           |
| C7  | Declared non-goals: no AI feature, no settings page, no VR, no 3D game, no iOS application.                                                      | Charter §5                                 | AI essay summarization (already on the Won't list), a reader preferences screen, and any native mobile client. Phone support is responsive web only.                               |
| C8  | Scope-cut trigger fires if all prior milestones are not complete by Sunday 8 November.                                                           | Scoping decision §10                       | Deciding what to cut in the moment. The order is fixed in advance: commenting first, then the essay file tree.                                                                     |

## 11. Assumptions

| #   | Assumption                                                                                                           | Owner      | Verify by                                             | Consequence if false                                                                                                                                                                                                                                               |
| --- | -------------------------------------------------------------------------------------------------------------------- | ---------- | ----------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| A1  | There will only ever be one Author/Administrator.                                                                    | Luke Witte | 2026-10-04 (end of design)                            | There is no way to distinguish or revoke credentials, because there is only one admin password. Every simplification in the auth design — no user table, no roles, no reset flow — would have to be reopened, and the design phase is already closed by this date. |
| A2  | Readership stays in the tens per day.                                                                                | Luke Witte | 2026-12-06 (first weeks of live traffic)              | The free-tier limits on Vercel and Turso start to bind, and FR-SYS-02's sixty-second cache refresh needs revisiting — for cost rather than for correctness.                                                                                                        |
| A3  | Essays are text only, with no pictures, diagrams, or other media.                                                    | Luke Witte | 2026-09-27 (end of Week 5)                            | The current Markdown display method no longer works. Images cannot be written to Vercel's read-only filesystem, so a blob storage service would be needed, and no requirement in §5 covers media.                                                                  |
| A4  | The learning curve for the technologies in this Capstone is manageable within the estimates.                         | Luke Witte | 2026-10-25 (end of Week 9, walking skeleton complete) | The commenting feature is cut first, per the cut order. If the overrun is larger than that saves, the essay file tree goes next.                                                                                                                                   |
| A5  | Converting the existing Word documents to Markdown preserves italics, headings, and footnotes without manual repair. | Luke Witte | 2026-09-27 (end of Week 5)                            | Each essay needs re-marking by hand before it can be pasted, at roughly thirty minutes per essay. This is unbudgeted, and with ten-plus essays it is a full working session that has to come out of construction hours.                                            |
| A6  | Comment volume stays manageable for one person to approve.                                                           | Luke Witte | 2026-12-13 (end of Week 16)                           | Approving comments becomes a chore rather than a feature. The length caps in FR-CMT-02 and the rate limiting in FR-CMT-03 would prove insufficient on their own, and no filtering service is in scope.                                                             |

## 12. Dependencies

| #   | Dependency                                          | Pinned to                                                                                                                                                  | Failure mode                                                                                                                                                                         | Fallback                                                                                                                                                                                                                                                            |
| --- | --------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| D1  | Vercel Free Tier, for hosting the site at no cost.  | Hobby plan, one developer, free deploys. Verified 2026-09-06 (Dependency-Verification Table).                                                              | Build minutes or bandwidth exhausted under unexpected traffic; the free tier's terms change mid-semester; a platform outage takes the site down entirely.                            | Deploy the same commit to Netlify's or Render's free tier — rehearsed and timed in Week 14, so this is a tested route rather than a hope. Failing that, pay for a higher tier, which breaks constraint C3.                                                          |
| D2  | Pandoc, for converting Word documents to Markdown.  | Record the output of `pandoc --version` in the repository so the working version is known. Installed on the desktop; must also be installed on the laptop. | Conversion silently drops footnotes or mangles italics on a particular document; Pandoc is not installed on whichever machine I am working from.                                     | Paste plain text and re-mark footnotes and italics by hand, at roughly thirty minutes per essay (this is assumption A5 turning false). For future essays, write directly in Markdown and skip the conversion entirely.                                              |
| D3  | npm and Next.js, for building and running the site. | Exact versions frozen in a committed `package-lock.json`; the Node major version declared in `package.json` under `engines`.                               | The npm registry is unreachable, so no install succeeds; a transitive package publishes a breaking version; a Next.js major release changes behavior the site relies on.             | The committed lockfile plus a retained local `node_modules` reproduces the last known-good build without touching the network. No major version upgrade of any dependency during weeks 9–12, regardless of what the changelog promises.                             |
| D4  | Turso, for storing essays, comments, and tags.      | Free plan, 5 GB storage, verified 2026-09-13. Schema changes tracked as migration files committed to the repository.                                       | Connection or storage limits reached; a service outage, which makes every page fail because all content lives there; an accidental destructive migration against the wrong database. | Essay text survives as Word originals outside the system, and the seed script rebuilds from those — rehearsed against the development database in Week 13 under NFR-REL-02. A local libSQL file can run the site for a demonstration if the hosted service is down. |

## 13. Obligations

### License position

**Code:** MIT (SPDX: `MIT`). `LICENSE` is present at the repository root.
**Essay text:** All rights reserved. The essays in this repository and in the
deployed database are my own work and are not covered by the MIT grant, which
applies to source code only. This is stated in `LICENSE` beneath the MIT text
and repeated in `README.md`.
**Reasoning:** MIT is the least friction for the maintainer persona in #2, who
needs to clone, run, and extend the code without asking permission. I do not
extend that permission to the essays themselves, because MIT would allow
commercial republication of my writing with only an attribution notice.

### Third-Party Obligations

- (Only a Possibility) https://philosophersapi.com Last Verified: 9-20-2026
- Vercel Free Tier Hosting https://vercel.com/home Last Verified: 9-20-2026
- Turso Database Free Tier https://docs.turso.tech/introduction Last Verified: 9-20-2026
- Next.js Docs https://nextjs.org/learn?utm_source=next-site&utm_medium=homepage-cta&utm_campaign=home Last Verified: 9-20-2026
