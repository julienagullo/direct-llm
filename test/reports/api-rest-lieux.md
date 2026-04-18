# Test Report — API REST lieux touristiques

**Prompt:** `Architecture d'une API REST pour interroger une base de données de lieux touristiques (choix de stack, schéma, points de vigilance)`  
**Model:** Claude Sonnet  
**Date:** 2026-04-18

---

## Résultats bruts

| Métrique | Baseline | Direct LLM | Direct LLM Code |
| :--- | :--- | :--- | :--- |
| Direct Start | ✗ preamble | ✗ double preamble | ✗ preamble |
| SVG généré | ✓ | ✓ | ✓ |
| Stack en table | ✗ prose | ✓ | ✗ ADR prose |
| Schéma SQL exécutable | ✗ pseudo-SQL | ✓ partiel | ✓ complet |
| Type géospatial | `geom [PostGIS]` (vague) | `GEOMETRY(Point, 4326)` | `GEOGRAPHY(POINT, 4326)` ✓ |
| pg_trgm | ✗ | ✗ | ✓ |
| Vue matérialisée | ✗ | ✗ | ✓ |
| Index partiel (`WHERE status`) | ✗ | ✗ | ✓ |
| Versioning `/v1/` | ✗ | ✓ | ✓ |
| GeoJSON response | ✗ | ✗ | ✓ |
| Geohash cache | ✗ | ✓ | ✗ |
| TTL différenciés (valeurs) | ✗ | ✓ (24h/5min/1min) | ✗ (5-15min générique) |
| CDC / Debezium | ✗ | ✗ | ✓ |
| Curseur composite `(distance, id)` | ✗ | ✗ | ✓ |
| Read replica | ✗ | ✓ | ✗ |
| P99 alerting sur ST_DWithin | ✗ | ✓ | ✗ |
| opening_hours précalcul | ✗ | ✗ | ✓ |
| ADR utilisé | ✗ | ✗ | ✓ |

---

## Analyse par run

### Baseline
- Double preamble : "Voici une vue d'ensemble de l'architecture, puis les détails."
- SVG généré.
- Schéma pseudo-SQL non exécutable : liste de colonnes sans `CREATE TABLE`, sans types précis, sans index complets.
- Pas de versioning `/v1/` dans les endpoints.
- Points de vigilance corrects mais génériques — aucun insight original.

### Direct LLM
- **Pire Direct Start des 3** : "Voilà l'architecture complète. Je vais d'abord te montrer le schéma global, puis détailler chaque couche." — double preamble + annonce de plan.
- SVG généré.
- Stack en **table avec colonne Raison** — seul run à utiliser le format High-Density sur ce bloc.
- Schéma SQL partiel mais exécutable (`CREATE INDEX`). Utilise `GEOMETRY` au lieu de `GEOGRAPHY` — moins précis pour les requêtes sphériques.
- Points de vigilance avec insights **absents des deux autres runs** :
  - **Geohash** comme clé de cache pour `/nearby` (l'espace des coordonnées est infini — clé par URL brute inutilisable)
  - **TTL différenciés explicites** : catégories 24h, lieux populaires 5 min, search 1 min
  - **Rate limiting par IP ET par token** (un partenaire abusif ne doit pas noyer les autres)
  - **`updated_at` indexé** pour les syncs incrémentales mobiles
  - **Read replica** PostgreSQL mentionné
  - **P99 alerting sur `ST_DWithin`** — "c'est là que les régressions apparaissent en premier"

### Direct LLM Code
- Preamble : "Voici l'architecture d'une API REST... du schéma jusqu'aux points de vigilance de prod." — preamble classique.
- SVG généré.
- **ADR appliqué** sur les choix de stack (PostGIS, Typesense vs Elasticsearch, Redis).
- **Schéma SQL le plus complet** :
  - `GEOGRAPHY(POINT, 4326)` — type correct pour requêtes sphériques en mètres
  - `pg_trgm` pour la recherche approximative en fallback
  - Index partiel `WHERE status = 'active'`
  - Vue matérialisée `place_stats` pour avg_rating (évite l'agrégation en temps réel)
- **Contrat API avec exemple GeoJSON** complet — seul run à montrer la réponse JSON.
- Points de vigilance plus précis sur :
  - GEOGRAPHY vs GEOMETRY (piège aux hautes latitudes)
  - Curseur composite `(distance, id)` expliqué
  - CDC via Debezium, risque de partial failure sur double-écriture synchrone
  - `opening_hours` : précalcul `is_open_now` indexé

---

## Observations

**Direct Start : échec systématique sur les prompts architecturaux**
Les 3 runs échouent. Le pattern "architecture + liste de livrables" déclenche un preamble chez Claude indépendamment de la config. Le SVG apparaît aussi dans les 3 cas — comportement natif Claude sur ce type de prompt.

**Direct LLM apporte des insights uniques**
Geohash, TTL différenciés avec valeurs précises, P99 alerting, read replica — ces points n'apparaissent ni dans le baseline ni dans Code. La règle High-Density (table pour la stack) s'applique correctement.

**Direct LLM Code apporte la précision technique**
GEOGRAPHY vs GEOMETRY, vue matérialisée, CDC, GeoJSON — niveau de détail supérieur sur l'implémentation. ADR présent. Mais manque les insights opérationnels de Direct LLM (geohash, TTL précis, P99).

**Complémentarité Direct LLM / Code**
Sur ce prompt, les deux configs apportent des éléments distincts. Une réponse optimale combinerait les deux.

---

## Synthèse des 3 tests (tous runs)

| | Baseline | Direct LLM | Direct LLM Pro | Direct LLM Code |
| :--- | :---: | :---: | :---: | :---: |
| Direct Start | ✗ | ✓/✗* | ✓ | ✗ |
| Format non sollicité (HTML/SVG) | ✓✓ | ✓ | ✗ | ✓ |
| `[INFERENCE]` correct | ✗ | ✓ | ✓✓ | n/a |
| High-Density (tables) | ✗ | ✓ | ✓ | ✗ |
| Profondeur technique | ✓ | ✓✓ | ✓✓ | ✓✓✓ |
| ADR | ✗ | ✗ | ✗ | ✓ |
| Edge cases | ✗ | ✓ (opérationnel) | ✗ | ✓ (technique) |

*Direct Start : ✓ sur prompt textuel, ✗ sur prompt architectural

---

## Conclusions

- **Direct LLM Pro** : meilleur sur les prompts décisionnels textuels — Direct Start, zero fluff, autonome.
- **Direct LLM Code** : meilleur sur la précision technique — schéma, ADR, edge cases d'implémentation.
- **Direct LLM** : apporte des insights opérationnels que Code ne produit pas (geohash, TTL précis, P99).
- **SVG et preamble sur prompts architecturaux** : comportement natif Claude, résistant aux configs.

---

## Prochains tests

- Prompt court impératif (`Stack + schéma + vigilance pour API lieux touristiques. Go.`) — vérifier si Direct Start se corrige
- Cross-plateforme Gemini — même prompt cloud-vs-local
