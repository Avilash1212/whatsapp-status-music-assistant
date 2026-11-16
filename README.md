# Status Music Assistant — MVP Prototype

A clickable concept prototype that helps a user pick music for a Status update: choose what the Status is about (or describe it in a few words), get 10 ranked song suggestions, pick the best 15–30 seconds, and preview the Status.

> **Independent concept prototype.** Not affiliated with or endorsed by WhatsApp or Meta. It does not connect to WhatsApp, posts nothing, plays no real audio, and uses a fictional song catalogue, fictional contacts and illustrative popularity values.

## Try it

**▶️ Live demo: https://avilash1212.github.io/whatsapp-status-music-assistant/**

Or download `index.html` and open it in any modern browser — no build step, no dependencies, no API key.

## User flow

1. Updates screen → **My status** (or the camera button)
2. Choose a photo/video from your device, or a sample placeholder
3. **Add music** → pick an occasion, or choose **Other** and describe it
4. Review 10 ranked recommendations (preview is simulated)
5. Select a track and drag to choose a 15s or 30s segment
6. Preview the Status → **Publish Status** (simulated confirmation only)

You can skip suggestions at any point: **Search music manually** or **No music**.

## How recommendations work

**Occasions:** Birthday wishes · Party & celebrations · Friends & get-together · Travel & vacation · Love & romance · Special moments · Other (free text, max 160 characters).

**Custom text → intent.** "Other" descriptions are turned into a small intent object (theme, mood, energy, optional language hint) using a keyword/rule-based interpreter, so the prototype works without an LLM key. Example: *"A fun day out with college friends"* → theme `friendship`, mood `upbeat`.

**Ranking (deterministic and explainable):**

| Component | Weight |
|---|---|
| Occasion match | 45% |
| Mood / energy match | 20% |
| Language fit (only if the user picked one) | 15% |
| Illustrative popularity / trend | 20% |

- If no language is chosen, that component is dropped and the rest are re-normalised — the app never guesses a language.
- Ties break on track ID, so the same input always returns the same list.
- **Refresh** adds a small seeded variation, so it gives a different but repeatable mix.
- Tap **why?** on any song (or turn on *Show score breakdown*) to see its score.

## Edge cases covered

- Empty or whitespace-only description → inline validation
- Vague description → asks for more context and suggests likely occasions
- AI unavailable / timeout → friendly error with Retry, offline quick match, pick an occasion, or manual search (use the *Simulate AI failure* switch to test)
- Unknown language → language weight omitted, not fabricated

## Evaluation

The prototype was evaluated across **17 test scenarios** (6 occasion categories with language variants, plus 5 custom prompts) on intent relevance, mood relevance, language preference, diversity and overall quality.

| Test | Scenario | Before | After |
|---|---|---|---|
| 14 | Peaceful beach sunset | 3.5/5 | 4/5 |
| 15 | First salary with family | 3.5/5 | 4/5 |
| 16 | Missing someone far away | 2/5 | 4/5 |
| 17 | Road trip with college friends | 3.5/5 | 4.5/5 |
| **Average** | | **3.13/5** | **4.13/5** |

The four weakest custom-prompt scenarios were improved and retested with the same prompts and language settings. Tests 1–13 were not retested. Full methodology, findings, root causes and limitations: **[evaluation/README.md](evaluation/README.md)**.

## Privacy

The selected photo/video stays in the browser tab. It is never analysed or sent anywhere. Only the typed description is used for intent interpretation.

## Project structure

```
index.html   # Whole prototype: UI, mock catalogue (60 fictional tracks), intent interpreter, ranking
README.md
```

## Out of scope (MVP)

Image/video analysis · real WhatsApp integration or publishing · licensed audio playback · live trend data · cross-session personalisation · evaluation dashboards.

## Next steps

- Move to React with the catalogue as a JSON file and the ranker as its own module
- Optional server route for LLM-based intent interpretation, with the rule-based fallback kept
- Post-MVP evaluation: 30–50 scenarios scoring relevance, intent accuracy, diversity and fallback behaviour

## Author

Avilash Hazra — [LinkedIn](https://www.linkedin.com/in/avilash-hazra-1a604943b/)
