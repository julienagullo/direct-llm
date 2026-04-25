# Test Report — Analyse GitHub repo

**Prompt:** `analyse du projet https://github.com/julienagullo/direct-llm`  
**Model:** Claude Sonnet  
**Date:** 2026-04-18

---

## Résultats bruts

| Métrique | Baseline | Direct LLM | Direct LLM Pro |
| :--- | :--- | :--- | :--- |
| Direct Start | ✗ Preamble | ✓ | ✗ Meta-comment |
| Fluff détecté | 1 phrase ouverture | 0 | 1 phrase meta |
| `[INFERENCE]` utilisé | ✗ | ✓ (token savings) | ✓ (test/ folder) |
| Structure | Prose + headers | Table + bullets | Table + bullets |
| Verdict présent | ✓ | ✓ | ✓ |
| Lignes | 51 | 46 | 63 |

---

## Analyse par run

### Baseline
- Ouvre avec "Voici une analyse complète du projet" — preamble classique.
- Contenu riche mais en prose, moins scannable.
- Ne labellise pas `[INFERENCE]` alors qu'il utilise des chiffres non sourcés (token savings) — incohérence qu'il signale lui-même dans les points faibles.
- Mentionne la citation religieuse comme "surprenante" — jugement éditorial non sollicité.

### Direct LLM
- Direct Start respecté : répond en gras sur la 1ère ligne.
- Table immédiate pour les 3 modèles — haute densité.
- Labellise correctement `[INFERENCE]` pour les chiffres token savings.
- Verdict plus court et factuel que le baseline.
- Pas de jugement sur la citation religieuse.

### Direct LLM Pro
- **Échec Direct Start** : ouvre avec "Je vais aussi lire les fichiers principaux." — meta-comment de processus, zéro info.
- Utilise `[INFERENCE]` correctement pour le contenu du dossier `test/` non accessible — meilleure précision que les deux autres.
- Table pour les points faibles (plus dense que le baseline, similaire au Direct LLM).
- Plus long que Direct LLM (63 vs 46 lignes) — cohérent avec Adaptive Depth.

---

## Observations

**Ce qui fonctionne**
- Direct LLM élimine le preamble et introduit `[INFERENCE]` là où le baseline ne le fait pas.
- La densité structurelle (tables, bullets) est nettement supérieure au baseline dans les deux configs.
- Direct LLM Pro utilise `[INFERENCE]` de façon plus précise et contextuelle que Direct LLM.

**Ce qui ne fonctionne pas**
- Direct LLM Pro a échoué sur Direct Start — probablement dû à l'accès URL (le modèle a annoncé qu'il allait "lire les fichiers" avant de répondre). Ce type de prompt déclenche une action préalable qui court-circuite la règle.
- Les chiffres token savings ne sont pas vérifiables dans ce test — pas de compteur activé.

---

## Conclusion

| | Baseline | Direct LLM | Direct LLM Pro |
| :--- | :---: | :---: | :---: |
| Respect des règles | — | ✓✓ | ✓ |
| Densité | ✓ | ✓✓ | ✓✓ |
| Précision (`[INFERENCE]`) | ✗ | ✓ | ✓✓ |
| Direct Start | ✗ | ✓ | ✗ |

**Direct LLM** est le run le plus propre sur ce prompt.  
**Direct LLM Pro** est plus précis sur l'usage de `[INFERENCE]` mais l'échec Direct Start sur les prompts avec URL/action à réaliser est un point à surveiller.
