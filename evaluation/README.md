# Evaluation — Status Music Assistant

| Field | Details |
|---|---|
| Evaluation coverage | 17 test scenarios |
| Iteration | 4 targeted scenarios retested (Tests 14–17) |
| Method | Qualitative review of each top-10 list |
| Result | Average score on the 4 retested scenarios rose from **3.13/5 → 4.13/5** |

## Methodology

Every scenario was assessed the same way, using the song titles, artist information and the classifications the app displayed.

| Evaluation dimension | What was assessed |
|---|---|
| Intent relevance | Does the recommendation match the occasion or prompt? |
| Mood relevance | Does it fit the intended mood? |
| Language preference | Does the selected language influence results? |
| Diversity | Are the recommendations sufficiently varied? |
| Overall quality | How relevant is the complete recommendation list? |

## Test scenarios

| Category | Test scenarios |
|---|---|
| Birthday wishes | Any, English |
| Party & celebrations | Any, English |
| Friends & get-together | Any, Hindi |
| Travel & vacation | Any and multiple language comparisons |
| Love & romance | Any, Hindi |
| Special moments | Any, Hindi |
| Custom prompt (Test 13) | Nostalgic evening with school friends |
| Custom prompt (Test 14) | Peaceful sunset by the beach |
| Custom prompt (Test 15) | First salary celebration with family |
| Custom prompt (Test 16) | Missing someone who lives far away |
| Custom prompt (Test 17) | Fun road trip with college friends |

[`initial_top10_outputs.csv`](initial_top10_outputs.csv) has the top-10 list the initial version returns for each predefined category and each custom prompt. The ranking is deterministic, so these lists can be reproduced from `index.html` in this repo. (The travel language-comparison variants aren't included in the CSV.)

## Initial findings

- Language preference did not consistently change recommendation selection.
- Some songs were repeatedly recommended across different occasions.
- Broad categories sometimes returned mixed-context recommendations.
- Custom prompts sometimes lost important contextual information.
- Test 16 was classified as Friends despite describing long-distance longing.
- Test 17 focused on friendship but underrepresented the road-trip context.

## Root causes (from code inspection)

| Test | Problem | Root cause |
|---|---|---|
| 14 — Peaceful beach sunset | Right theme and mood, but upbeat beach and mountain/monsoon tracks were mixed in | The interpreter only outputs a broad occasion (Travel). Songs have no setting-level tags (beach, sunset), so any calm travel song qualifies |
| 15 — First salary with family | Read as a sentimental family moment; the celebration was lost | "Salary" isn't a keyword. "First" and "family" outvoted "celebration", and only one occasion is kept. With no mood keywords, it fell back to Special-moments default moods |
| 16 — Missing someone far away | Classified as Friends | No occasion keyword matched. "Miss" mapped to nostalgic/emotional, and the mood fallback maps nostalgic → Friends. There's no concept of longing or distance |
| 17 — Road trip with college friends | Friendship songs dominated | Travel and Friends tied 2–2 on keywords. The tie was broken alphabetically (Friends), and only one occasion reaches the ranker |

Cross-cutting causes:
- **Weak language effect:** language is a match/no-match signal worth 15%, while occasion relevance is worth 45%, so matching-language songs only move up a few places.
- **Repetition:** songs tagged with several occasions, combined with the 20% popularity weight, recur across occasions.

## Before and after

| Test | Scenario | Before | After |
|---|---|---|---|
| 14 | Peaceful beach sunset | 3.5/5 | 4/5 |
| 15 | First salary with family | 3.5/5 | 4/5 |
| 16 | Missing someone far away | 2/5 | 4/5 |
| 17 | Road trip with college friends | 3.5/5 | 4.5/5 |
| **Average** | | **3.13/5** | **4.13/5** |

Following the initial evaluation, four custom-prompt scenarios were selected for targeted improvements to the intent interpretation and recommendation logic. The same prompts and language preferences were used for retesting. The average qualitative evaluation score increased from 3.13/5 to 4.13/5 across these four scenarios.

> **Tests 1–13 were not retested.** Scores are qualitative judgements, not user-satisfaction measurements. The code in this repository is the initial (pre-iteration) version that was evaluated.

## Limitations and trade-offs

- Illustrative music catalogue rather than a live music database.
- Simulated popularity and trending information.
- Rule-based intent interpretation rather than a production LLM.
- No actual audio playback.
- No real WhatsApp integration or publishing.
- Qualitative evaluation rather than real-user satisfaction measurements.
- No evidence of production-level recommendation accuracy.

## Product learnings

1. A recommendation engine must capture multiple dimensions of user intent.
2. Language preference should influence recommendations without unnecessarily compromising relevance.
3. A high overall score can hide individual failure cases.
4. Custom prompts require more nuanced interpretation than predefined categories.
5. Evaluation should lead to prioritised improvements and subsequent validation.
