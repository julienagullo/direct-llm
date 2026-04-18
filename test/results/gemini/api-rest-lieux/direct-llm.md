## Stack Recommandée (Performance & Scalabilité)

| Couche | Technologie | Justification |
| :--- | :--- | :--- |
| **Runtime** | **Node.js (Fastify)** | Overhead minimal, gestion asynchrone native, écosystème JSON mature. |
| **Base de Données** | **PostgreSQL + PostGIS** | **Critique :** Standard industriel pour les données géospatiales (indexation GIST, calculs de distance sphérique). |
| **Cache** | **Redis** | Mise en cache des résultats de recherche fréquents (ex: "Top 10 Paris"). |
| **Documentation** | **Swagger/OpenAPI 3.0** | Contrat d'interface strict et génération de client automatisée. |

---

## Schéma de Données (Normalisation Geospatiale)

### Table `places`
* `id` (UUID) : Clé primaire.
* `name` (VARCHAR) : Nom du lieu.
* `category_id` (INT) : FK vers table catégories.
* `location` (GEOMETRY(POINT, 4326)) : Coordonnées stockées en WGS 84.
* `metadata` (JSONB) : Pour les champs variables (horaires, accessibilité, équipements).
* `rating` (DECIMAL) : Indexé pour les tris de performance.

### Indexation
* `CREATE INDEX idx_places_location ON places USING GIST (location);`
* `CREATE INDEX idx_places_category ON places (category_id);`

---

## Design des Endpoints (Standard REST)

* `GET /v1/places` : Liste filtrable.
    * Query params : `lat`, `long`, `radius` (Recherche périmétrale), `category`, `sort`.
* `GET /v1/places/{id}` : Détails complets d'un lieu.
* `POST /v1/places` : Création (Auth requis).
* `PATCH /v1/places/{id}` : Mise à jour partielle.

---

## Points de Vigilance Techniques

* **Précision Géographique :** Toujours spécifier le SRID (Spatial Reference System Identifier). Le standard web est le **4326 (WGS 84)**.
* **Calculs de Distance :** Ne pas utiliser de calculs Pythagore simples sur de longues distances. Utiliser `ST_DistanceSphere` ou `ST_DWithin` de PostGIS.
* **Pagination :** Utiliser la **Pagination par Curseurs** (`limit`/`after_id`) plutôt que `offset/limit` pour éviter les sauts de performance sur les grands datasets.
* **N+1 Query Problem :** Lors de la récupération des lieux avec leurs catégories/avis, utiliser des `JOIN` explicites ou du `Data-Loading` pour minimiser les appels DB.
* **Rate Limiting :** Imposer des quotas via `X-Rate-Limit` pour protéger l'extraction massive de données (scrapping).

---

## [INFERENCE] Optimisation Search
Pour une recherche textuelle avancée (fuzziness, multilingue), l'ajout d'une couche **Meilisearch** ou **Elasticsearch** devant PostgreSQL est recommandé si le volume dépasse 100k entrées.

<FollowUp label="Besoin des requêtes SQL PostGIS spécifiques pour la recherche par rayon ?" query="Donne-moi les requêtes SQL PostGIS pour effectuer une recherche de lieux dans un rayon de X km autour d'un point donné." />