## Cloud LLM vs Local LLM — Décision pour un dev

| Critère | Cloud (abonnement) | Local (ex: Ollama + Llama/Qwen) |
|---|---|---|
| **Setup** | 0 min | 15–60 min |
| **Coût fixe** | ~20–200€/mois | GPU upfront (ou 0 si CPU ok) |
| **Coût marginal** | Tokens → ça monte vite | ~0 |
| **Perf modèle** | SOTA (GPT-4o, Claude, Gemini) | 70B max raisonnable, gap réel |
| **Latence** | Réseau + file | Dépend du hardware |
| **Confidentialité** | Données sortent | 100% local |
| **Offline** | ❌ | ✅ |
| **Fine-tuning** | Rare/cher | Natif (LoRA, QLoRA) |
| **Contexte long** | 200K+ tokens | Limité par VRAM/RAM |
| **Intégration IDE** | Continue.dev, Copilot, Cursor | Continue.dev, LM Studio |

---

## Les vrais facteurs de décision

**→ Prends le cloud si :**
- Tu bosses sur des projets complexes (archi, debug profond, refacto massive) — le gap de qualité sur les gros modèles est encore réel
- Tu veux zéro friction, rester dans le flow
- Budget pro ou facturé au client
- Pas de contrainte data souveraineté

**→ Prends le local si :**
- Données sensibles / NDA / code propriétaire critique
- Volume de requêtes élevé (autocomplétion en continu, batch processing)
- Tu veux expérimenter : fine-tuning, RAG custom, agents locaux
- Contexte no-internet (air gap, voyage)
- Hardware dispo : ≥16 Go RAM (CPU ok pour 7-14B), idéalement GPU 24 Go VRAM pour 70B Q4

---

## Stack recommandé si local

- **Runtime** : Ollama (le plus simple)
- **Modèles** : Qwen2.5-Coder 32B (code), Mistral/Mixtral (général), Llama 3.3 70B (qualité max)
- **IDE** : Continue.dev (VSCode/JetBrains)
- **UI** : Open WebUI

---

## Verdict pragmatique

**Hybride** est souvent la réponse : local pour l'autocomplétion + tâches routinières (coût → 0), cloud pour les tâches complexes ponctuelles. Cursor/Continue permettent de router selon le contexte.

Quel est ton hardware actuel et ton use case principal ?