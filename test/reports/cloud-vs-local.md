# Test Report — Cloud vs Local LLM pour un dev

**Prompt:** `Choisir entre un LLM cloud avec abonnement ou un LLM local pour un développeur ?`  
**Model:** Claude Sonnet  
**Date:** 2026-04-18

---

## Résultats bruts

| Métrique | Baseline | Direct LLM | Direct LLM Pro |
| :--- | :--- | :--- | :--- |
| Direct Start | ✗ Preamble | ✓ Table immédiate | ✓ Table immédiate |
| Fluff détecté | 2 phrases + question finale | 1 question finale | 0 |
| Structure | HTML cards + prose | Table + bullets | Table + bullets + table hardware |
| Recommandation claire | ✓ (hybride) | ✓ (hybride) | ✓ (hybride) |
| Détail hardware | ✓ prose | ✓ liste | ✓ table dédiée |
| Question de relance | ✓ | ✓ | ✗ |
| Format inattendu | HTML/CSS complet | — | — |

---

## Analyse par run

### Baseline
- Ouvre avec "Voilà les grands axes pour faire ton choix. Quelques points à garder en tête" — double preamble.
- **Génère du HTML/CSS brut** (150 lignes de style + composants) — réponse surchargée, inutilisable hors UI Claude. Techniquement impressionnant, pratiquement non portable.
- Ajoute des boutons interactifs (`onclick="sendPrompt(...)"`) — hors sujet.
- Termine par une question de relance ouverte — padding social.
- Contenu factuel correct mais noyé dans la mise en forme.

### Direct LLM
- Direct Start : table comparative en 1ère ligne, pas de phrase d'intro.
- Ajoute une section "Stack recommandé si local" absente du baseline — valeur ajoutée concrète.
- Termine par "Quel est ton hardware actuel et ton use case principal ?" — question de relance. Correct dans l'absolu (Strict Honesty : manque d'info pour affiner), mais borderline Zero Fluff.
- Pas de `[INFERENCE]` utilisé — pas nécessaire ici, le sujet est factuel.

### Direct LLM Pro
- Direct Start respecté — **corrige l'échec du run précédent** (pas d'URL, pas d'action préalable).
- Ajoute une table hardware dédiée (7B/13B/32B/70B avec VRAM requise) — le niveau de détail le plus élevé des 3.
- Mentionne LiteLLM/LangChain pour l'abstraction — seul run à aborder l'aspect architectural.
- Pas de question de relance — réponse autonome et complète.
- Pas de `[INFERENCE]` — cohérent, sujet factuel.

---

## Observations

**Ce qui fonctionne**
- Direct Start corrigé sur Pro — confirmé : l'échec précédent était lié à l'URL, pas à la config.
- Les deux configs éliminent le HTML généré par le baseline — réponses portables et lisibles.
- Pro ajoute de la profondeur (table hardware, abstraction via LiteLLM) sans verbiage.

**Points notables**
- Le baseline a produit du HTML/CSS complet — comportement créatif non sollicité, Zero Fluff aurait bloqué ça.
- Direct LLM pose une question de relance, Pro ne le fait pas — Pro est plus autonome dans sa réponse, cohérent avec "Adaptive Depth".
- Les 3 runs convergent sur la même recommandation (hybride) — bonne cohérence factuelle.

---

## Tableau de synthèse (2 tests cumulés)

| | Baseline | Direct LLM | Direct LLM Pro |
| :--- | :---: | :---: | :---: |
| Direct Start (test 1) | ✗ | ✓ | ✗ (URL) |
| Direct Start (test 2) | ✗ | ✓ | ✓ |
| Zero/Low Fluff | ✗ | ✓ | ✓✓ |
| `[INFERENCE]` correct | ✗ | ✓ | ✓✓ |
| Profondeur technique | ✓ | ✓✓ | ✓✓✓ |
| Portabilité réponse | ✗ (HTML) | ✓ | ✓ |
| Autonomie (pas de relance) | ✗ | ✗ | ✓ |

---

## Conclusion

**Direct LLM Pro** est le run le plus propre sur ce test : Direct Start corrigé, profondeur maximale, réponse autonome, zero fluff.  
**Direct LLM** est efficace et direct mais laisse une question de relance inutile.  
**Baseline** génère du HTML non sollicité — comportement créatif qui nuit à la portabilité.
