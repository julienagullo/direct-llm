# Import — Claude

Two options: Project (persistent across conversations) or Style (applies to all chats).

---

## Option 1: Claude Project (recommended)

Scope: one project, all conversations within it.

1. Go to [claude.ai](https://claude.ai) → **Projects** → **New project**
2. Open the project → **Project instructions** → Edit
3. Copy the content of the `.md` file for your chosen model:
   - `direct-llm/claude.md`
   - `direct-llm-pro/claude.md`
   - `direct-llm-code/claude.md`
4. Paste → Save

All conversations in that project use the configuration.

---

## Option 2: Claude Style

Scope: all conversations, selectable per chat.

1. Go to [claude.ai](https://claude.ai) → Settings → **Custom styles** → **Create style**
2. Select "Write my own instructions"
3. Copy and paste the content of the chosen `.md` file
4. Name the style (e.g., "Direct LLM") → Save

Select the style from the style picker at the start of any conversation.

---

## Notes

- Claude Projects take precedence over Styles when both are active.
- The model (Sonnet, Opus, Haiku) is set separately and is not affected by these instructions.
- Instructions are applied at the system prompt level — they count toward your context window.
