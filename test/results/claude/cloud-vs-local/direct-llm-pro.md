## Cloud vs Local LLM pour un dev — tableau de décision

| Critère | Cloud (API) | Local (Ollama, llama.cpp…) |
|---|---|---|
| **Qualité modèle** | ✅ State-of-the-art (GPT-4o, Claude, Gemini) | ⚠️ Bons mais en retard (Llama 3.3, Qwen, Mistral) |
| **Latence** | Variable (réseau) | Faible si bon hardware |
| **Coût fixe** | 0 (pay-as-you-go possible) | GPU/RAM upfront |
| **Coût variable** | Élevé à gros volume | ~0 après achat |
| **Confidentialité** | ❌ Données sortent | ✅ 100% local |
| **Setup** | Immédiat | Moyen à élevé |
| **Offline** | ❌ | ✅ |
| **Fine-tuning facile** | ❌ (rare, coûteux) | ✅ |
| **Contexte long** | ✅ (128k–1M tokens) | ⚠️ Limité par RAM/VRAM |
| **Multimodal** | ✅ | Partiel |

---

## Recommandation selon profil

**→ Choisis le cloud si :**
- Tu prototypes, tu testes, tu veux la meilleure perf sans friction
- Tes données ne sont pas sensibles
- Ton usage est sporadique (coût marginal faible)
- Tu veux du contexte long ou du multimodal

**→ Choisis le local si :**
- Données sensibles / confidentielles (code propriétaire, données clients)
- Usage intensif et répétitif (le coût cloud explose)
- Tu veux fine-tuner ou modifier le comportement du modèle
- Tu as déjà un bon GPU (≥16 Go VRAM pour des modèles 7–13B corrects)
- Besoin offline ou air-gapped

---

## Approche pragmatique (souvent la meilleure)

**Hybride :** local pour le dev/test + cloud pour la prod ou les tâches critiques.

- Ollama en local pour itérer sans coût
- API cloud (Anthropic, OpenAI) pour les features qui nécessitent la meilleure qualité
- Abstraire via LiteLLM ou LangChain pour switcher sans réécrire

---

## Hardware minimum si local

| Modèle | VRAM requise | Qualité |
|---|---|---|
| 7B quantisé (Q4) | 6–8 Go | Correct |
| 13B quantisé | 10–12 Go | Bon |
| 32B quantisé | 20–24 Go | Très bon |
| 70B quantisé | 40+ Go ou CPU lent | Excellent |

Sans GPU dédié → cloud ou CPU très lent (inutilisable pour dev).