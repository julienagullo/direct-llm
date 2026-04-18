Je vais aussi lire les fichiers principaux.

## Analyse — `julienagullo/direct-llm`

### Ce que c'est

Un **framework de configuration de prompts système** (pas du code exécutable) — des fichiers Markdown à coller dans les interfaces Claude, Gemini, ChatGPT pour modifier le comportement de base du modèle.

---

### Structure du repo

| Fichier | Rôle |
|---|---|
| `direct-llm.md` | Modèle de base — vitesse, densité |
| `direct-llm-pro.md` | Modèle avancé — stratégie, nuance |
| `direct-llm-code.md` | Modèle dev — architecture, code |
| `docs/` | Instructions d'import par plateforme |
| `test/` | [INFERENCE] probablement des prompts de test de comportement |

---

### Concept central

3 règles partagées entre tous les modèles :

- **Direct Start** — pas de préambule, la réponse en premier
- **High-Density Formatting** — bullets, gras, tableaux
- **Strict Honesty** — flag les questions mal posées, préfixe les inférences avec `[INFERENCE]`

La différenciation entre les 3 variantes porte sur : profondeur, ton, traitement de l'inconnu, edge cases, ADR (Architecture Decision Records).

---

### Points forts

- **Idée pertinente** : les modèles LLM perdent effectivement beaucoup de tokens en padding social et préambules inutiles. Réduire ça a un impact réel sur la densité d'information.
- **Multi-plateforme** : Claude, Gemini, ChatGPT — bon choix de couverture.
- **Segmentation claire** des personas (consultant / advisor / dev) — chaque profil a une utilité distincte.
- **MIT License** — libre d'utilisation et de modification.

---

### Limites & points faibles

| Problème | Impact |
|---|---|
| **Pas de code** — c'est 3 fichiers Markdown | Difficile de versionner ou tester automatiquement les comportements |
| **Pas de CI/tests** | Impossible de savoir si une mise à jour casse un comportement attendu |
| **Claim token quantifié sans source** | "50–150 tokens par requête" / "7,5–22,5 milliards/mois" — non vérifiable, marketing |
| **Citation religieuse dans le README** | Hors-sujet technique, peut rebuter des contributeurs |
| **0 stars, 0 forks, 1 contributeur** | Projet très jeune ou peu visible |
| **Pas de changelog ni de versioning** | Évolution du framework opaque |

---

### Verdict

**Utilité réelle** : oui, pour quelqu'un qui veut un system prompt structuré et réutilisable sans partir de zéro. Le travail de segmentation en 3 personas est bien pensé.

**Originalité** : faible — ce type de "prompt template" existe en masse (ex. AIPRM, AwesomeChatGPT prompts, etc.). La valeur ajoutée ici est la rigueur éditoriale et la clarté des règles.

**Recommandation** : bon point de départ à adapter à ton contexte. À surveiller si le dépôt évolue avec des tests comportementaux ou des benchmarks comparatifs.