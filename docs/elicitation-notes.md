# Elicitation Interview Guide

A thirty-minute script for the one conversation Milestone 3 requires. Print it,
fill it in by hand, then transcribe the notes into your repository the same day.
Memory decays faster than you think it does.

---

## Before you sit down

- **Pick a real human** who has the problem. Not a classmate being polite. Not you.
- **Say the boundary out loud:** "I am not going to build everything you say. I am
  trying to understand what actually happens today." This buys you honest answers
  instead of a wish list.
- **Bring nothing to show.** A prototype turns an interview into a review. Later.
- **Record the date.** A requirement without a date is a rumor.

**Interviewee:** Brandon Carter **Role:** Philosophy Student
**Date:** 9-12-2026  **Duration:** 14 minutes  **Consent to quote (y/n):** y

*Disclaimer:* I did not conduct this as a traditional interview. I missed our first planned meeting time when track event came up so I emailed the questions to him and he responded to each one. On the first question I added spaces for the "And then what?" to see if it would prompt extra thinking.

---

## The eight questions

Ask them in this order. The order matters: present tense before future tense,
behavior before opinion.

1. **"Walk me through the last time you did this. Start from the beginning."**
   You want the *episode*, not the summary. Interrupt only to ask "and then what?"

   "I wanted to find a paper that talked about Luther's view on apologetics. I used my laptop to go to Standford Encyclopedia of Philosophy and I search for "Luther on apologetics". It brought up like 10 different essays and I read through the titles."

   *And then what?* 
   
   "Most of them weren't about Luther at all. There was one actual entry on Martin Luther, and then things like "Fideism," "Natural Theology," "Philosophy of Religion" — stuff where Luther is probably mentioned in a paragraph. I opened the Luther entry and Ctrl-F'd "apologetics." I think it hit twice, and neither hit was really about it. So I skimmed the sections on reason and the theology of the cross and pulled the footnotes."

    *And then what?* 

   "So I went to the CUW library's site, which logged me out twice, and eventually got into JSTOR. I found two articles that looked right from the abstract. One turned out to be about Melanchthon with three pages on Luther. The other was actually good but from 1974, and I couldn't tell if the argument had been superseded."

   *And then what?* 

   "At some point I realized that the real problem was that Luther never uses the word "apologetics." It's a more modern word. So keyword searching is basically broken for this. I had to switch to searching for the things he says instead like "reason as the devil's whore," the Heidelberg Disputation, theology of the cross versus theology of glory, the fight with Erasmus over the bondage of the will. That worked better, but by then I'd used up a lot of the evening."

2. **"What did you use to do it?"**
   A spreadsheet, a whiteboard, a group chat, a paper list, nothing. Whatever it
   is, that is your competition and your data model.

   "Pretty much just browser tabs and bookmarks. I search through sites like Standford and JSTOR and save tabs open while Im actually working. Then I save them as bookmarks so that I can have quick access later."

3. **"Where did that go wrong the last time?"**
   Failures are specific; satisfaction is vague. This question produces requirements.

    "One thing, like I was mentioning earlier, sometimes the words you're looking for like apologetics don't actually appear in the paper because they didn't use the word that we use. They would talk about the topic, but not specifically use the word for one reason or another. I've also lost sources before. I'd close a tab, then two days later remember "hey, there was a good line about the deus in that other paper" and now I have no idea where I read it. Browser history search is often useless because everything is jstor.org/stable/1234567."

4. **"What did you do when it went wrong?"**
   The workaround is a feature request wearing a disguise.

    "Citation chasing, mostly. I'd find one decent article and keep looking at its bibliography, then occasionally look at the bibliographies of those if they seemed interesting. It works, but it takes you backwards in time and you keep hitting dead ends with a bunch of nonsense topics. For the losing-sources problem I started just pasting every URL into the Google Doc with no notes, this worked, it just made my word docs cluttered sometimes depending upon how many sources I needed. I have also tried asking ChatGPT for article recommendations. This sometimes works, but I have to really be careful. Usually like two of the four don't exist. The titles sounded perfectly plausible, which was worse than getting nothing. Now I just use it to explain a concept I'm stuck on, never to find a source. I do sometimes just email my professor and ask "what should I be reading on this?" and they usually respond quick with sources they've used. These at least you can trust because they reviewed them themselves. Usually they don't have quite enough for whatever paper I'm writing."

5. **"How often does this happen? How long does it take?"**
   Numbers. Push for a number even if it is a guess, then write "estimated."

    Big source hunts, maybe 4 times a semester, one per major paper, so estimated 8 a year. Smaller versions for weekly reading responses, call it once a week during term. The first session, the one I described was an estimated 2 and a half to 3 hours before I had anything I trusted. The whole gathering phase before I start writing, is usually 8 to 12 hours spread over a week or so. This is typically opening things that turn out to be irrelevant, re-finding things I already had, and fighting with the CUW library.

6. **"Who else touches this?"**
   You have just found a stakeholder you had not listed.

   "My professor is really the only other one that deals with my makeshift sources on the word docs. Usually he doesn't though because he only needs final draft sources."

7. **"If this problem disappeared tomorrow, what would change about your day?"**
   The answer is the rationale line for half your requirements.

   "I'm not sure how it would really disappear. I guess if you just mean a faster way to find sources, it would make the intial phase of gathering sources much shorter. I could spend less time searching and more time actually reading sources more fully."

8. **"What is the part I have not asked about?"**
   Ask it. Then be quiet for a full ten seconds. The silence does the work.

   "I have a fear sometimes that there's one canonical article on this that everyone in the field knows about, and I'm going to hand in a paper without it and look like I didn't do the work. I always try to do broad research and find all the main ones. I have no way to check that I really find everything though. Sometimes, given two articles, one in a journal I've heard of and one I haven't, I have no real basis for ranking them other than vibes and publication date. My professor can do that in two seconds. I can't, and nothing I use helps me learn how."

---

## Questions to avoid, and why

| Do not ask | Why | Ask instead |
|---|---|---|
| "Would you use an app that…?" | Everyone says yes to a hypothetical. | "What do you do today?" |
| "Do you want feature X?" | You have handed them your design to rubber-stamp. | "Where does it go wrong?" |
| "How should this work?" | You are outsourcing the job you are being graded on. | "What has to be true for this to be worth opening?" |
| "Is this important?" | Every feature is important in the abstract. | "If you could only have one of these two, which?" |

---

## After the interview — same day, within an hour

- [ ] Transcribe raw notes. Do not clean them up yet; keep the words they used.
- [ ] Mark every sentence as **F** (a fact about today), **W** (a want), or **O** (an opinion).
      Facts become requirements first. Wants get triaged. Opinions get a rationale line.
- [ ] Circle every noun they used more than twice. Those nouns are your data model.
- [ ] Write down the three things you assumed before the interview that are now wrong.
- [ ] Add one row to the Open Questions table for anything you could not answer.
- [ ] Commit the notes with a dated message.

---

## The observation pass (do this too, if you can)

Twenty minutes of watching beats an hour of asking. Sit with them while they do
the task. Write down only what you see:

| Time | What they did | What they said | What surprised me |
|---|---|---|---|

The "what surprised me" column is where the requirements nobody would have
thought to ask for come from.