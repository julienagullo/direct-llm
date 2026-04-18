# Direct LLM

Prompt configuration framework for Claude, Gemini and ChatGPT.
Eliminates verbosity, maximizes information density, reduces token consumption.

## Models

| Model | Persona | Focus |
| :--- | :--- | :--- |
| **Direct LLM** | Senior Consultant | Speed / Density |
| **Direct LLM Pro** | Senior Advisor | Strategy / Nuance |
| **Direct LLM Code** | Senior Developer | Architecture / Dev |

## Shared Rules

- **Direct Start** — No preamble. Lead with the answer.
- **High-Density Formatting** — Bullet points, bold concepts, tables.
- **Strict Honesty** — Flag flawed questions. State assumptions. Prefix speculation with `[INFERENCE]`.

## Differences by Model

| Rule | Direct LLM | Direct LLM Pro | Direct LLM Code |
| :--- | :--- | :--- | :--- |
| Fluff | Zero | Low (clarity preserved) | — |
| Unknown | "I don't know" | "Unknown" + next steps | State assumptions |
| Depth | Fixed | Adaptive to complexity | Architecture-first |
| Recommendations | — | When decision implied | — |
| Edge cases | — | — | By default |
| Schema / API | — | — | Before implementation |
| ADR | — | — | Complex choices |
| Tone | Abrasive ok | Not abrasive | Senior engineer |

## Token Impact

Measured across 5 tests on Claude Sonnet 4 and Gemini 3 Flash:

| Platform | Prompt type | Reduction |
| :--- | :--- | :--- |
| Claude | Decisional | −74% to −76% |
| Claude | Textual analysis | −24% to −42% |
| Gemini | Decisional | −9% to −14% |
| Gemini | Technical | −32% |
| Direct LLM Code | Architectural | +19% (expected — config pushes depth) |

Gemini baselines are naturally less verbose; gains are smaller.  
Direct LLM Code increases token count by design — more useful information per token, less filler prose.

**At 1M users / 100 req / month:** ~15B tokens saved (conservative, decisional prompts only).  
See `test/reports/global.md` for methodology and raw data.

## Import

1. Pick the model that fits your use case
2. Copy the content of the corresponding file into your platform:

   - **Claude** → Project instructions or Custom style
   - **Gemini** → Gem manager → New Gem → Instructions
   - **ChatGPT** → Custom GPT → Configure → Instructions

```
direct-llm.md        → base model
direct-llm-pro.md    → advanced model
direct-llm-code.md   → developer model
```

See `docs/` for platform-specific import instructions.

## Responsibility

Author disclaims any responsibility for the use that is made with this tool.

```text
Al-Nu’man ibn Bashir reported,
The Messenger of Allah (Peace and Blessings be upon Him) said: « Verily, the lawful is clear and the unlawful is clear, and between the two of them they are doubtful matters about which many people don't know. Thus, he who avoids doubtful matters clears himself in regard to his religion and his honor, and he who falls into doubtful matters will fall into the unlawful as the shepherd who pastures near a sanctuary, all but grazing there in. Verily, every king has a sanctum and the sanctum of Allah is his prohibitions. Verily, in the body is a piece of flesh which, if sound, the entire body is sound, and if corrupt, the entire body is corrupt. Truly, it is the heart. »
Sahih al-Bukhārī 52, Sahih Muslim 1599
```

```text
D'après Nu'man Ibn Bachir (qu'Allah l'agrée),
Le Messager d'Allah (que La Prière d'Allah et Son Salut soient sur Lui) a dit : « Certes le halal est clair et certes le haram est clair et il y a entre les deux des choses ambiguës que peu de gens connaissent. Celui qui s'écarte des choses ambiguës a préservé sa religion et son honneur. Quant à celui qui tombe dans les choses ambiguës il tombe dans le haram comme le berger qui fait paitre ses bêtes près d'un enclos réservé et qui sont sur le point de rentrer dedans. Certes chaque roi a un domaine réservé et certes le domaine réservé d'Allah est ses interdits. Certes il y a dans le corps un morceau de chair, si il est bon alors l'ensemble du corps est bon tandis que si il est mauvais alors c'est l'ensemble du corps qui est mauvais, certes il s'agit du coeur. »
Sahih al-Bukhārī 52, Sahih Muslim 1599
```

## License

Copyright © jagullo.fr

Licensed under the MIT license.