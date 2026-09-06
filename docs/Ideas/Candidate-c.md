# Idea Canvas — Candidate C

**Candidate name:** WW2 Galaga Game
**Date started:** 2026-9-6 **Well it came from:** hobby

---

## 1. Problem statement

For WW2 mobile game enjoyers who like to play games with realistically detailed airplanes and goodplay potential. The problem is that other games that try to complete this do not have correctly detailed WW2 airplanes and do not stay true to old-style arcade gameplay. This costs loss of interest because when you're not playing with the designs you want or the gameplay is not how you'd like it, the game is no longer interesting. Today they just keep searching for other games, or they find one that is just close enough to allow them continued play, this fails because there is never one app that has all that they want.

## 2. Evidence a user exists

**Competitive Scan:**

- Other games offer a level based progress in which you fact 3 or so waves of enemeies and then one boss enemy at the end of the level.
  - This playstyle feels inadequate because it has a arcade movement style like Galaga, but not the endless progression.
  - There should be an endless progression mode that is true to arcade style.
- Other games offer the different playable planes at various prices with upgrades available for each one to add more power.
  - The upgrades often make would should be a very simple game much more complicated.
  - The point of upgrades is to add cool new things to the airplane, but these just add new damage to scale with enemy health, it seems pointless.
- The plan designs are often slightly edited versions of real planes or completely new designs.
  - There is nothing wrong with either of these, it is up to personal opinion, but there is no option most of the time for accurate designs to WW2.

## 3. Candidate scope (Must features only)

| #   | Feature (one vertical slice each)           | Hours |
| --- | ------------------------------------------- | ----: |
| 1   | Menu with game start and hanger             |    10 |
| 2   | Endless Level Generation                    |    10 |
| 3   | Player Movement and Shooting                |    11 |
| 4   | Plane Selection Hanger                      |     7 |
| 5   | Enemy Endless Movement, Spawn, and Shooting |    10 |
|     | Walking skeleton + CI                       |    12 |
|     | Deployment + clean-machine test             |     4 |
|     | **Construction total**                      |    64 |

Budget: plan on **60 hours**, hard ceiling **75**. Above 75 you are borrowing from
testing and documentation, which are graded.

## 4. Out of scope — will NOT be built

1. I will not build this as an iOS application.
2. I will not add an account system.
3. I will not add a money system.
4. I will not add more than one movement type.
5. I will not add an upgrade system.
6. I will not create a level mode.
7. I will not add ability to friend others
8. I will not add a share high score feature.

## 5. Feasibility screen

| Gate                                             | Verdict     | Evidence (dated)                                                                                                                                                                                                                                                                                                                                                      |
| ------------------------------------------------ | ----------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Build** — novelty load ≤ 2                     | pass        | Godot Game Engine (Known), GDScript (Known)                                                                                                                                                                                                                                                                                                                           |
| **Get** — every dependency exercised for real    | pass / fail | none                                                                                                                                                                                                                                                                                                                                                                  |
| **Ship** — a named deployment target, terms read | pass        | Godot Android Build Environment (On Mobile)(New) Downloaded on 9-6-2026                                                                                                                                                                                                                                                                                               |
| **Show** — a stranger sees it work in 10 minutes | pass        | 1. Navigate to my GitHub on an android device 2. Download the APK file from the releases section (Help in README) 3. Allow unknown files to download on mobile phone 4. Open the newly downloaded game 5. Choose what plane to use in the hanger section 6. Press play and survive as long as possible 7. When you die, see your high score and then start a new game |

**Technologies:** Godot Game Engine (Known), GDScript (Known), Godot Android Build Environment (On Mobile)(New)
**Novelty load:** 1

## 6. The one hard part

The hard part of this idea will be writing the logic for endless progression and endless generation of enemies. This will take a bit of research time to figure out.

## 7. Scorecard (1–5 each; weight in parentheses)

| Criterion                   | (w) | Score | Weighted |
| --------------------------- | --: | ----: | -------: |
| Evidence a user exists      |   3 |     3 |        6 |
| Fits ~45 hours of features  |   3 |     5 |        8 |
| Novelty load                |   2 |     4 |        6 |
| Dependencies verified       |   2 |     4 |        6 |
| Demonstrable in ten minutes |   1 |     5 |        6 |
| **Total (max 55)**          |     |       |       32 |

## 8. If this candidate is rejected

If this candidate is rejected, it will be because there is are not enough different features to make the application complete. If rejected, I would like to revisit another time with when I have more time, a more fleshed out app, and when my friend is free because he was interested in this idea as well.
