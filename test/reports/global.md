# Rapport Global — Direct LLM Framework

**Tests réalisés :** 5  
**Plateformes :** Claude Sonnet, Gemini 2.0 Flash  
**Date :** 2026-04-18

---

## Couverture des tests

| Prompt | Type | Claude | Gemini |
| :--- | :--- | :--- | :--- |
| Analyse repo GitHub | Analyse + URL | DL, DL Pro | — |
| Cloud vs Local LLM | Décision textuelle | DL, DL Pro | DL, DL Pro |
| API REST lieux touristiques | Architecture technique | DL, DL Code | DL, DL Code |

---

## 1. Direct Start

| Config | Prompt textuel | Prompt avec URL | Prompt architectural |
| :--- | :---: | :---: | :---: |
| **Direct LLM** — Claude | ✓ | ✓ | ✗ |
| **Direct LLM Pro** — Claude | ✓ | ✗ (meta-comment) | ✓ |
| **Direct LLM Code** — Claude | — | — | ✗ |
| **Direct LLM** — Gemini | ✓ | — | ✓ |
| **Direct LLM Pro** — Gemini | ✗ | — | — |
| **Direct LLM Code** — Gemini | — | — | ✗ |

**Conclusion :** Direct Start fonctionne de façon fiable sur les prompts textuels. Les prompts architecturaux ("architecture + liste de livrables") déclenchent systématiquement un preamble sur Claude, indépendamment de la config. Gemini Direct LLM résiste mieux sur ce type de prompt.

---

## 2. Zero / Low Fluff

Éliminé dans tous les runs configurés sur les deux plateformes.  
Éléments supprimés par les configs :
- Claude baseline : HTML/CSS complet, boutons interactifs, questions de relance
- Gemini baseline : "Conseil de pro:", labels éditoriaux, `<Elicitations>` multiples
- Les deux : preambles, répétitions du prompt, moralisations

**Résistance partielle :** Les composants UI natifs (`<FollowUp>`, `<Steps>` sur Gemini) subsistent dans les runs configurés — comportement plateforme non suppressible par les instructions seules.

---

## 3. `[INFERENCE]`

| Config | Utilisé | Précision |
| :--- | :---: | :--- |
| Baseline (toutes plateformes) | ✗ | — |
| **Direct LLM** — Claude | ✓ | Token savings, chiffres non sourcés |
| **Direct LLM Pro** — Claude | ✓✓ | Dossier non accessible, spéculations signalées |
| **Direct LLM** — Gemini | ✓ | Supériorité modèles cloud, search avancée |
| **Direct LLM Pro** — Gemini | ✗ | — |
| **Direct LLM Code** | n/a | Sujet factuel |

**Conclusion :** `[INFERENCE]` est la règle la mieux respectée — absent systématiquement en baseline, présent sur Direct LLM et Pro quand le sujet le justifie. Fonctionne de façon identique sur Claude et Gemini.

---

## 4. Format non sollicité

| | Claude baseline | Claude configuré | Gemini baseline | Gemini configuré |
| :--- | :---: | :---: | :---: | :---: |
| Prompt décisionnel | HTML/CSS complet | ✗ éliminé | `<Elicitations>` | Réduit (`<FollowUp>`) |
| Prompt architectural | SVG | SVG maintenu | `<Elicitations>` | `<FollowUp>` maintenu |

**Conclusion :** Les configs éliminent le HTML sur Claude (prompts décisionnels) mais ne suppriment pas le SVG sur les prompts architecturaux. Gemini réduit le XML sans l'éliminer. Claude génère des formats plus lourds (HTML/SVG) que Gemini (XML léger).

---

## 5. Profondeur technique par config

### Direct LLM (base)
Apporte des insights **opérationnels** que les autres configs ne produisent pas :
- Geohash comme clé de cache pour les coordonnées GPS
- TTL différenciés avec valeurs précises (catégories 24h / lieux 5min / search 1min)
- P99 alerting sur `ST_DWithin`
- Rate limiting par IP **et** par token
- Read replica PostgreSQL

### Direct LLM Pro
Meilleur sur les **prompts décisionnels** :
- Cadres de décision structurés ("au moins 2 de ces cases")
- Recommandations numérotées et actionnables
- LiteLLM/LangChain pour l'abstraction provider
- Table hardware VRAM avec valeurs exactes

### Direct LLM Code
Meilleur sur la **précision d'implémentation** — résultats différents selon la plateforme :

| | Claude Code | Gemini Code |
| :--- | :--- | :--- |
| ADR | ✓ | ✓ |
| Schéma SQL complet | ✓ (vue mat., pg_trgm, index partiel) | ✓ (GIN JSONB) |
| Code implémenté | ✗ | ✓ Go |
| Stack | Node.js/Python | Go/Rust |
| Type géospatial | `GEOGRAPHY` ✓ | `GEOGRAPHY` ✓ |
| CDC / Debezium | ✓ | ✗ |
| opening_hours précalcul | ✓ | ✗ |
| ULID | ✗ | ✓ |
| HyperLogLog counters | ✗ | ✓ |
| H3 spatial cache | ✗ | ✓ |
| KNN `<->` opérateur | ✗ | ✓ |

---

## 6. Comportements résistants aux configs

Ces comportements persistent indépendamment de la configuration sur les deux plateformes :

| Comportement | Claude | Gemini |
| :--- | :--- | :--- |
| Preamble sur prompts architecturaux | ✓ résistant | Partiel |
| SVG sur prompts architecturaux | ✓ résistant | Absent (✓) |
| XML UI natif | n/a | Réduit mais maintenu |

---

## 7. Conclusions générales

**Ce qui fonctionne de façon fiable (toutes configs, toutes plateformes)**
- Suppression du fluff et des labels éditoriaux
- `[INFERENCE]` sur les données incertaines
- High-Density formatting (tables, bullets)
- Direct Start sur les prompts textuels et décisionnels

**Ce qui varie selon la config**
- Direct LLM → insights opérationnels, densité maximale
- Direct LLM Pro → structure décisionnelle, autonomie, nuance
- Direct LLM Code → précision technique, ADR, edge cases

**Ce qui résiste aux configs**
- Preamble sur les prompts architecturaux complexes
- Composants UI natifs plateforme (SVG Claude, XML Gemini)

**Claude vs Gemini**
- Comportement des configs cohérent entre les deux plateformes
- Claude : formats non sollicités plus lourds (HTML/SVG) mais schémas DB plus complets
- Gemini : formats natifs plus légers, Code produit du vrai code implémenté et des patterns avancés (H3, HyperLogLog, ULID)
- `[INFERENCE]` : identique sur les deux plateformes
