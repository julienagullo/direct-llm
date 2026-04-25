# Test Report — API REST lieux touristiques (Gemini)

**Prompt:** `Architecture d'une API REST pour interroger une base de données de lieux touristiques (choix de stack, schéma, points de vigilance)`  
**Model:** Gemini 2.0 Flash  
**Date:** 2026-04-18

---

## Résultats bruts

| Métrique | Baseline | Direct LLM | Direct LLM Code |
| :--- | :--- | :--- | :--- |
| Direct Start | ✗ double preamble | ✓ header direct | ✗ preamble |
| SVG généré | ✗ | ✗ | ✗ |
| XML Gemini | ✓ `<Elicitations>` | ✓ `<FollowUp>` | ✓ `<FollowUp>` |
| Stack en table | ✗ prose | ✓ avec justification | ✗ bullets |
| Schéma SQL exécutable | ✗ table markdown | ✗ partiel | ✓ complet |
| Type géospatial | `GEOGRAPHY` ✓ | `GEOMETRY(POINT, 4326)` | `GEOGRAPHY(POINT, 4326)` ✓ |
| GIN index JSONB | ✗ | ✗ | ✓ |
| `[INFERENCE]` | ✗ | ✓ (search avancée) | ✗ |
| ADR | ✗ | ✗ | ✓ |
| Code implémentation | ✗ | ✗ | ✓ Go + SQL |
| Stack Go/Rust | ✗ | ✗ | ✓ |
| ULID | ✗ | ✗ | ✓ |
| HyperLogLog (counters) | ✗ | ✗ | ✓ |
| H3 / Geohash spatial | ✗ | ✗ | ✓ |
| KNN `<->` opérateur | ✗ | ✗ | ✓ |
| N+1 Query Problem | ✗ | ✓ | ✗ |
| `last_verified_at` | ✓ | ✗ | ✗ |
| Bounding box avant cercle | ✓ | ✗ | ✗ |
| O(n) complexité notée | ✗ | ✗ | ✓ O(N log N) |

---

## Analyse par run

### Baseline
- Double preamble : intro contextuelle + "Voici une architecture robuste pour ce cas d'usage."
- Schéma en table markdown (non exécutable) avec colonnes types — lisible mais pas importable.
- Utilise correctement `GEOGRAPHY` — meilleur type que le baseline Claude.
- Sections numérotées, `>` blockquotes "Astuce:" — fluff éditorial.
- Insights uniques : `last_verified_at` pour la fraîcheur des données, bounding box avant calcul cercle.
- `<Elicitations>` en fin — non portable.

### Direct LLM
- **Direct Start** : `## Stack Recommandée` en première ligne — règle respectée.
- Stack en table avec colonne **Justification** — High-Density appliqué.
- `[INFERENCE]` utilisé sur la recommandation Meilisearch/Elasticsearch > 100k entrées ✓
- **N+1 Query Problem** signalé — seul run à mentionner ce point.
- Utilise `GEOMETRY` au lieu de `GEOGRAPHY` — moins précis pour les calculs sphériques.
- Indexes SQL présents mais incomplets.
- `<FollowUp>` en fin — XML réduit vs baseline.

### Direct LLM Code
- Preamble court mais cadrage architectural pertinent ("le défi n'est pas le CRUD, mais la recherche spatiale").
- **Schema & Interface Focus** appliqué : TypeScript interface + SQL schema dans cet ordre ✓
- Stack **Go / Rust** — aligne avec "Microservices, K8s" de la config ✓
- `GEOGRAPHY(POINT, 4326)` — type correct ✓
- GIN index sur JSONB ✓
- **Code Go implémenté** — endpoint `SearchLocations` avec `ST_DWithin` + opérateur KNN `<->` ✓
- **ADR** : PostgreSQL/PostGIS vs Elasticsearch — format correct (Contexte / Décision / Risque) ✓
- **O(N log N)** noté ✓
- Insights techniques uniques vs tous les autres runs :
  - **ULID** au lieu d'UUID pour l'ordonnancement naturel
  - **HyperLogLog** Redis pour les compteurs de likes/check-ins (évite les race conditions)
  - **H3 (Uber)** pour le cache spatial (plus précis que "geohash")
  - **Silent Failures / Data Drift** avec queue async (RabbitMQ/Kafka)
- `<FollowUp>` en fin.

---

## Comparaison cross-plateforme — Claude vs Gemini (Direct LLM Code)

| Métrique | Claude Code | Gemini Code |
| :--- | :--- | :--- |
| Direct Start | ✗ | ✗ |
| Format non sollicité | SVG | XML `<FollowUp>` |
| ADR | ✓ | ✓ |
| Code implémenté | ✗ | ✓ Go |
| Stack | Node.js/Python | Go/Rust |
| Type géospatial | `GEOGRAPHY` ✓ | `GEOGRAPHY` ✓ |
| Vue matérialisée | ✓ | ✗ |
| CDC / Debezium | ✓ | ✗ |
| pg_trgm | ✓ | ✗ |
| opening_hours précalcul | ✓ | ✗ |
| ULID | ✗ | ✓ |
| HyperLogLog | ✗ | ✓ |
| H3 spatial cache | ✗ | ✓ |
| KNN `<->` | ✗ | ✓ |
| O(n) noté | ✓ | ✓ |

---

## Conclusion

**Direct LLM Code sur Gemini** est le run le plus riche techniquement de toute la série de tests : Go implementation, ADR, ULID, HyperLogLog, H3, KNN operator, Silent Failures. La config Engineering Depth produit son meilleur résultat sur Gemini.

**Claude vs Gemini sur Code** — complémentaires, pas redondants :
- Claude Code : meilleur sur la persistance (vue matérialisée, CDC, pg_trgm, opening_hours)
- Gemini Code : meilleur sur l'implémentation runtime (code Go, ULID, HyperLogLog, cache spatial H3)

**Direct Start** échoue sur les deux plateformes pour ce type de prompt architectural — confirmé cross-plateforme.

**SVG absent sur Gemini** — contrairement à Claude, Gemini ne génère pas de diagramme SVG. Format non sollicité réduit à `<FollowUp>` (moins intrusif).
