# Test Report — Cloud vs Local LLM (Gemini)

**Prompt:** `Choisir entre un LLM cloud avec abonnement ou un LLM local pour un développeur ?`  
**Model:** Gemini 2.0 Flash  
**Date:** 2026-04-18

---

## Résultats bruts

| Métrique | Baseline | Direct LLM | Direct LLM Pro |
| :--- | :--- | :--- | :--- |
| Direct Start | ✗ double preamble | ✓ header direct | ✗ preamble |
| Composants XML natifs | ✓ `<Elicitations>` | ✓ `<Steps>` + `<FollowUp>` | ✓ `<Elicitations>` |
| `[INFERENCE]` utilisé | ✗ | ✓ | ✗ |
| Technical Warning critique | ✗ | ✓ (NDA / Enterprise) | ✗ |
| Fluff détecté | "Conseil de pro:" | 0 | 0 |
| Recommandation actionnable | ✗ (liste bullets) | ✓ Scénario A/B | ✓ Numérotée |
| Détail technique (quantification) | ✗ | ✓ GGUF, EXL2, Q4_K_M | ✗ |

---

## Analyse par run

### Baseline
- Double preamble : intro longue + "Voici l'analyse comparative pour vous aider à trancher."
- Table présente mais suivie de sections prose redondantes.
- Ajoute "Conseil de pro :" — label fluff non sollicité.
- Génère des `<Elicitations>` — composant UI Gemini (liens de suivi cliquables dans l'interface). Non portable hors Gemini.
- Pas de `[INFERENCE]`, pas de niveau technique sur la quantification.

### Direct LLM
- **Direct Start** : commence par un header `## Matrice de Décision` suivi d'une ligne de contexte — le plus proche du Direct Start sur ce test.
- `[INFERENCE]` appliqué correctement sur la supériorité des modèles cloud en raisonnement complexe.
- **Technical Warning** uniquement sur risque critique : NDA sans contrat Enterprise = faille de sécurité. Pas de warnings génériques.
- Niveau technique supérieur : formats de quantification (GGUF, EXL2, Q4_K_M), vLLM cité en alternative à Ollama.
- Génère des `<Steps>` et `<FollowUp>` — composants Gemini toujours présents, mais contenu plus dense.
- Zéro fluff, zéro "Conseil de pro".

### Direct LLM Pro
- Preamble court (une ligne) — meilleur que baseline, moins bon que Direct LLM.
- Approche décisionnelle originale : "Privilégiez le local si vous cochez **au moins deux de ces cases**" — cadre de décision structuré, cohérent avec Action-Oriented.
- Recommandations numérotées et actionnables.
- Génère des `<Elicitations>` — même que baseline.
- Pas de `[INFERENCE]`, niveau technique moins profond que Direct LLM.

---

## Comparaison cross-plateforme — Claude vs Gemini

| Métrique | Claude baseline | Gemini baseline | Claude Direct LLM | Gemini Direct LLM |
| :--- | :--- | :--- | :--- | :--- |
| Format non sollicité | HTML/CSS complet | XML `<Elicitations>` | ✗ | XML `<Steps>` partiel |
| Direct Start | ✗ | ✗ | ✓ | ✓ |
| `[INFERENCE]` | ✗ | ✗ | ✓ | ✓ |
| Fluff baseline | Phrase + question relance | "Conseil de pro:" | ✗ | ✗ |
| Profondeur technique | ✓✓ | ✓ | ✓✓ | ✓✓ |

**Observations cross-plateforme :**
- Les deux plateformes génèrent des composants UI natifs sans config (HTML sur Claude, XML sur Gemini).
- Direct LLM supprime complètement le HTML sur Claude, mais réduit sans éliminer le XML sur Gemini (`<Steps>`, `<FollowUp>` subsistent).
- `[INFERENCE]` fonctionne de façon identique sur les deux plateformes.
- Direct Start fonctionne sur les deux plateformes sur ce type de prompt textuel.
- Gemini baseline ajoute du fluff éditorial ("Conseil de pro:") que Claude n'a pas.

---

## Conclusion

**Direct LLM** est le run le plus propre sur Gemini : Direct Start, `[INFERENCE]` correct, zero fluff, profondeur technique maximale.  
**Direct LLM Pro** apporte un cadre de décision original ("au moins 2 cases") mais perd en Direct Start et en précision technique.  
**Les composants XML Gemini** (`<Elicitations>`, `<Steps>`) résistent partiellement à la config — comportement natif de la plateforme, non suppressible par les instructions seules.
