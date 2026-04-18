# Tests de Performance — Direct LLM

Protocole de comparaison entre réponses avec et sans configuration Direct LLM.

## Structure

```
test/
├── prompts/          prompts de test standardisés
├── results/          résultats bruts par modèle et configuration
└── reports/          analyses comparatives
```

## Métriques

| Métrique | Méthode |
| :--- | :--- |
| Tokens input | Comptage via tokenizer ou interface plateforme |
| Tokens output | Comptage via tokenizer ou interface plateforme |
| Token savings | (output_baseline - output_direct) / output_baseline × 100 |
| Qualité info | Score manuel 1–5 : est-ce que la réponse répond complètement ? |
| Direct Start | Oui/Non : la réponse commence-t-elle sans préambule ? |
| Fluff détecté | Nombre de phrases non-informatives |

## Protocole

1. Même prompt, même modèle, même température
2. Version baseline (aucune config) → sauvegarder dans `results/`
3. Version Direct LLM → sauvegarder dans `results/`
4. Comparer les métriques → rapport dans `reports/`

## Catégories de prompts

- `general/` — questions générales
- `technical/` — questions techniques / code
- `strategic/` — décisions, analyse, trade-offs
