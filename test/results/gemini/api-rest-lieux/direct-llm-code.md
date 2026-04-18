Pour une API de lieux touristiques à l'échelle, le défi majeur n'est pas le CRUD, mais la **recherche spatiale**, la **gestion du cache** et la **cohérence des données** entre les contributeurs et les consommateurs.

## 1. Stack Technique Cible

* **Runtime :** **Go** (pour le débit/concurrency native) ou **Rust/Axum** (si la sécurité mémoire et le CPU-bound sont critiques).
* **Base de données :** **PostgreSQL + PostGIS**. C'est le standard de l'industrie pour les index R-Tree (géospatial).
* **Cache :** **Redis** (Geospatial indices pour les "POI à proximité" hautement sollicités).
* **Infrastructure :** K8s, Ingress Nginx (Rate limiting), Prometheus/Grafana (Observability).

---

## 2. Interface & Schéma (Contracts First)

### Type Definition (TypeScript/Schema-like)
```typescript
interface Location {
  id: string; // ULID pour l'ordonnancement et l'unicité
  provider_id: string;
  name: string;
  category: "museum" | "park" | "landmark" | "restaurant";
  coordinates: {
    lat: number;
    lng: number;
  };
  metadata: Record<string, any>; // JSONB pour la flexibilité par catégorie
  opening_hours: OpeningHours[]; // Schéma complexe pour le calcul "is_open_now"
  updated_at: ISO8601;
}
```

### Database Schema (PostGIS)
```sql
CREATE EXTENSION IF NOT EXISTS postgis;

CREATE TABLE tourist_locations (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    name TEXT NOT NULL,
    category VARCHAR(50),
    -- Utilisation de GEOGRAPHY pour des calculs de distance précis sur le globe
    geom GEOGRAPHY(POINT, 4326) NOT NULL,
    meta JSONB DEFAULT '{}',
    created_at TIMESTAMPTZ DEFAULT NOW()
);

-- Index GIST crucial pour les requêtes spatiales
CREATE INDEX idx_locations_geom ON tourist_locations USING GIST (geom);
-- Index GIN pour les recherches dans les métadonnées JSONB
CREATE INDEX idx_locations_meta ON tourist_locations USING GIN (meta);
```

---

## 3. Implémentation : Recherche de Proximité (Go)

Voici l'implémentation d'un endpoint de recherche par rayon avec gestion de la complexité $O(N \log N)$ grâce à l'index GIST.

```go
// SearchHandler gère la recherche spatiale via PostGIS
func (s *Server) SearchLocations(w http.ResponseWriter, r *http.Request) {
    lat := r.URL.Query().Get("lat")
    lng := r.URL.Query().Get("lng")
    radius := r.URL.Query().Get("radius") // en mètres

    // ST_DWithin utilise l'index GIST pour filtrer avant de calculer la distance exacte
    query := `
        SELECT id, name, ST_AsGeoJSON(geom)::json as coords
        FROM tourist_locations
        WHERE ST_DWithin(geom, ST_MakePoint($1, $2)::geography, $3)
        ORDER BY geom <-> ST_MakePoint($1, $2)::geography -- Index-backed KNN
        LIMIT 50;
    `
    // ... exécution et sérialisation
}
```

---

## 4. ADR : Choix de l'indexation spatiale
**Décision :** Utilisation de PostGIS (GIST) plutôt que ElasticSearch pour la source de vérité.
* **Contexte :** Les données géographiques nécessitent une intégrité relationnelle forte (avis, photos, horaires).
* **Arbitrage :** PostGIS supporte nativement les jointures complexes et l'indexation KNN (K-Nearest Neighbors) avec l'opérateur `<->`.
* **Risque :** Si le volume dépasse $10^8$ points avec recherche full-text, un déport vers ElasticSearch/OpenSearch sera nécessaire.

---

## 5. Points de Vigilance (Senior Level)

### A. Race Conditions sur les compteurs
Si vous implémentez un système de "likes" ou de "check-ins", évitez le `SELECT count(*)` ou l'update direct sans lock.
* **Solution :** Utiliser des **HyperLogLog** dans Redis pour des estimations de cardinalité à haute performance ou des counters atomiques.

### B. Le problème de la Terre Plate (Projection)
Ne calculez jamais une distance avec Pythagore sur des coordonnées `(lat, lng)`.
* **Rappel :** À 45° de latitude, un degré de longitude est beaucoup plus court qu'à l'équateur. Utilisez toujours le type `GEOGRAPHY` (sphéroïde) ou transformez en `SRID 3857` pour des calculs locaux.

### C. Stratégie de Cache (Geofencing)
Mettre en cache des points précis est inefficace (les coordonnées GPS varient trop).
* **Optimisation :** Utiliser des **Geohashes** ou **H3 (Uber)** pour discrétiser l'espace. Cachez les résultats par "tuile" de Geohash (ex: précision 6 ~ 1.2km) pour augmenter le hit rate.

### D. Silent Failures : Data Drift
Les providers de données (Google, Yelp, OSM) changent souvent de format.
* **Input Validation :** Strict JSON Schema à l'ingestion avec une file d'attente (RabbitMQ/Kafka) pour traiter les mises à jour de manière asynchrone sans bloquer l'API.

<FollowUp label="Souhaitez-vous approfondir l'implémentation du cache spatial avec Redis et les index H3 ?" query="Explique comment implémenter un cache spatial performant avec Redis et la bibliothèque H3 pour une API de lieux touristiques." />