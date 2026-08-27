# AI Usage Log — <Capstone Project>

<!--
  Milestone 1 template. Copy this file into your repository as docs/ai-usage.md.
  The policy header is written ONCE, in Week 1, before you need it. The table
  grows one row per Amber-zone use, all semester, written the day it happens.
  This file is a required artifact in the Week 16 submission.
-->

**Owner:** <Luke Witte> · **Policy set:** <2026-26-26> · **Last entry:** <YYYY-MM-DD>

## Policy

**Spine rule.** The human stays in the loop where the judgment lives. AI accelerates;
I decide, I verify, and I am accountable for everything in this repository.

**The line I do not cross.** I will not delegate a judgment I cannot defend. If I
cannot explain a decision in this repository in my own words, under questions,
without the tool in front of me, it does not go in.

### Tools I have decided to use

| Tool / product | Model or version, as best I can name it | What I will use it for                                  | What I will never use it for                                                                                           |
| -------------- | --------------------------------------- | ------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------- |
| Claude Code    | Claude Opus or Sonnet                   | Debugging, Rework code solution, understanding new code | Generate work that I do not understand, work I could easily do myself in shorter time, writing what I think or believe |
|                |                                         |                                                         |                                                                                                                        |

### Zones

| Zone               | Covers                                                                                                                                                                                             | What I owe                                     |
| ------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------- |
| Green (assistive)  | error-message explanation, reformatting, grammar, boilerplate I fully understand, rubber-ducking a design I already drafted                                                                        | nothing; work normally                         |
| Amber (generative) | drafted requirements, scaffolded code I keep, generated tests, proposed architecture, documentation prose                                                                                          | one row in the table below, the day it happens |
| Red (prohibited)   | generated decision records, memos, or reflections submitted as mine; a choice I cannot defend; another person's private data or a classmate's unsubmitted work; code I cannot explain line by line | do not                                         |

### Disclosure

Every Amber-zone use appears below. Generated code that survives into `src/` carries a
comment naming the date of the log entry that covers it. Nothing in `docs/adr/`, the
memos, or the reflections is generated text.

## Entries

| Date    | Tool / model                | What I asked | What I kept | What I changed | How I verified |
| ------- | --------------------------- | ------------ | ----------- | -------------- | -------------- |
| 8-26-26 | I did not use AI in Week 1. | N/A          | N/A         | N/A            | N/A            |

<!--
  A BAD entry (do not imitate):
    | Week 3 | ChatGPT | requirements | most of it | some | looked fine |

  A GOOD entry:
    | 2026-09-22 | <assistant + the model version you actually used>
    | "Interview me about a household food-tracking app and list functional requirements."
    | 6 of 19 proposed requirements, as raw material only.
    | Rewrote all 6 into FR form with actor + condition; deleted 13 as out of scope
       (it invented multi-household sharing and a mobile app I never mentioned).
    | Checked each against my Week-2 scoping decision; confirmed FR-004's "3 days"
      threshold with my actual user instead of accepting the model's default.

  The good entry takes ninety seconds and is evidence of judgment.
  The bad one is evidence of nothing.
-->
