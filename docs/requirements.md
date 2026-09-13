# Software Requirements Specification — <Your Project Name>

<!--
COPY THIS FILE into your repository as docs/requirements.md and delete every
comment block as you fill it in. Keep the section numbering; the Week-16 rubric
and the Week-4 traceability matrix both key off it.
Requirement IDs are FR-<AREA>-<nn>. Assign an ID once and never reuse it.
Retire an ID by marking it Withdrawn; do not renumber.
-->

**Author:** <name> **Version:** 1.0 **Date:** <YYYY-MM-DD>
**Status:** Draft | In review | Baselined

---

## 1. Purpose and Scope

One paragraph: what this system is for, who it serves, and what problem it removes.
One paragraph: what is explicitly _outside_ the boundary of this release.

## 2. Stakeholders and Personas

| Persona           | Who they are   | What they need from the system | Evidence they exist                       |
| ----------------- | -------------- | ------------------------------ | ----------------------------------------- |
| <name, age, role> | <one sentence> | <one sentence>                 | <interview date / observation / artifact> |

Include the maintainer who inherits this repository. They are a stakeholder.

## 3. Definitions

Define every term your requirements use in a project-specific sense. If a reader
could interpret a word two ways, it belongs here.

| Term | Definition in this document |
| ---- | --------------------------- |

## 4. Assumptions and Dependencies

- **Assumption:** <something you are taking as true without proof> — _If false:_ <consequence>
- **Dependency:** <an external service, dataset, device, or person you rely on> — _If unavailable:_ <fallback>

## 5. Functional Requirements

<!-- Repeat this block for every requirement. Group by area. -->

### FR-<AREA>-<nn> — <short imperative name>

**Priority:** Must | Should | Could | Won't (this release)
**Requirement:** <Actor> shall be able to <action> <object> <under what condition>.
**Rationale:** Why this exists, and which persona asked for it.
**Acceptance criteria:**

- Given <starting state>, when <the actor does this>, then <this observable thing is true>.
- Given <edge or failure case>, when <trigger>, then <defined behavior>.

**Source:** <interview, observation, regulation, your own decision — name it>

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
