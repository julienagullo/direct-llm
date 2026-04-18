**direct-llm** — framework de configuration de prompts système pour LLMs.

---

## Ce que c'est

3 fichiers `.md` à copier-coller dans les instructions d'un modèle (Claude, Gemini, ChatGPT). Pas de code, pas de lib, pas d'API — c'est du prompt engineering packagé.

| Modèle | Persona | Cas d'usage |
|---|---|---|
| `direct-llm.md` | Senior Consultant | Réponses denses, rapides |
| `direct-llm-pro.md` | Senior Advisor | Stratégie, nuance, décision |
| `direct-llm-code.md` | Senior Developer | Archi, dev, ADR, edge cases |

---

## Règles communes aux 3 modèles

- **Direct Start** — Pas de préambule, réponse en premier
- **High-Density Formatting** — Bullets, gras, tableaux
- **Strict Honesty** — Questions mal posées signalées, spéculations préfixées `[INFERENCE]`

---

## Points forts

- **Économie de tokens** : 50–150 tokens économisés par requête en éliminant le padding social
- **Compatible multi-plateforme** : Claude (Project instructions), Gemini (Gem manager), ChatGPT (Custom GPT)
- **Segmentation claire** des personas selon le besoin (consultation / stratégie / technique)
- **Licence MIT** — réutilisable librement

---

## Points faibles / Limites

- **Aucun code exécutable** — c'est du texte brut, zéro automatisation, zéro CI
- **Non versionné fonctionnellement** — pas de releases, pas de changelog structuré
- **Effet non garanti** — les résultats dépendent du modèle cible et de sa conformité aux instructions système
- **Pas de tests objectifs** — le dossier `test/` existe mais son contenu n'est pas visible/publié ; les chiffres "50–150 tokens" et "7.5B tokens/month à 1M users" sont des [INFERENCE] non sourcées
- **Communauté quasi-nulle** — 0 stars, 0 forks, 1 contributeur au moment de l'analyse

---

## Verdict

Projet utile à titre personnel ou en équipe pour standardiser le comportement d'un assistant IA. Trop simple pour être qualifié de "framework" au sens logiciel du terme — c'est un **prompt template collection**. La valeur est réelle si tu travailles quotidiennement avec des LLMs et que tu veux des réponses moins verbeux par défaut.