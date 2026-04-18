# Tests — Direct LLM

Comparaison de réponses avec et sans configuration Direct LLM.

## Structure

```
test/
├── results/          réponses brutes par prompt et configuration
└── reports/          analyses comparatives
```

## Protocole

1. Même prompt, même modèle, même plateforme
2. Run baseline (aucune config) → `results/<prompt>/baseline.md`
3. Run(s) avec config → `results/<prompt>/direct-llm.md`, `direct-llm-pro.md`, `direct-llm-code.md`
4. Analyse → `reports/<prompt>.md`

## Métriques

| Métrique | Méthode |
| :--- | :--- |
| Direct Start | La réponse commence-t-elle sans préambule ? |
| Fluff détecté | Nombre de phrases non-informatives |
| Format non sollicité | HTML, SVG, boutons interactifs générés sans demande |
| `[INFERENCE]` | Correctement utilisé sur les données incertaines ? |
| Profondeur technique | Présence d'insights absents du baseline |

## Rapport global

`reports/global.md` — synthèse de tous les tests, toutes plateformes.

## Tests réalisés

| Prompt | Configs testées | Plateforme | Rapport |
| :--- | :--- | :--- | :--- |
| Analyse repo GitHub | baseline, direct-llm, direct-llm-pro | Claude | `reports/analyse-github.md` |
| Cloud vs Local LLM | baseline, direct-llm, direct-llm-pro | Claude | `reports/cloud-vs-local.md` |
| API REST lieux touristiques | baseline, direct-llm, direct-llm-code | Claude | `reports/api-rest-lieux.md` |
| Cloud vs Local LLM | baseline, direct-llm, direct-llm-pro | Gemini | `reports/cloud-vs-local-gemini.md` |
| API REST lieux touristiques | baseline, direct-llm, direct-llm-code | Gemini | `reports/api-rest-lieux-gemini.md` |
