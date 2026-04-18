# Import — Claude

Two options: Style (applies to all chats) or Project (persistent across conversations).

## Option 1: Claude Style (recommended)

Scope: all conversations, selectable per chat.

1. Go to [claude.ai](https://claude.ai) → **New conversation**
2. Click the **Search and tools** menu on left corner
3. Select **Create & edit styles.** in **Use style** option
4. Create a new style with **Create custom style.** button
5. Copy and paste the content of the chosen `.md` file
6. Name the style (e.g., "Direct LLM") → Save

Select the style from the style picker at the start of any conversation.

## Option 2: Claude Project

Scope: one project, all conversations within it.

1. Go to [claude.ai](https://claude.ai) → **Projects** → **New project**
2. Open the project → **Project instructions** → Edit
3. Copy the content of the `.md` file for your chosen model:
   - `direct-llm/claude.md`
   - `direct-llm-pro/claude.md`
   - `direct-llm-code/claude.md`
4. Paste → Save

All conversations in that project use the configuration.

## Notes

- Claude Projects take precedence over Styles when both are active.
- The model (Sonnet, Opus, Haiku) is set separately and is not affected by these instructions.
- Instructions are applied at the system prompt level — they count toward your context window.
