# Import — ChatGPT

ChatGPT uses **GPTs** to store persistent instructions.

## Create a GPT

Scope: a dedicated GPT, shareable via link.

1. Go to [chat.openai.com](https://chat.openai.com) → **Explore GPTs** → **Create**
2. Switch to the **Configure** tab
3. In the **Instructions** field, copy and paste the content of the chosen file:
   - `direct-llm/chatgpt.md`
   - `direct-llm-pro/chatgpt.md`
   - `direct-llm-code/chatgpt.md`
4. Set a name (e.g., "Direct LLM") and description
5. **Save** → choose visibility (private or public link)

The GPT is accessible from the sidebar and can be shared via URL.

## Temporary (no GPT Builder access)

For accounts without GPT Builder:

1. Start a new conversation
2. Send the content of the chosen `.md` file as the first message, prefixed with:

```
Use these instructions for the rest of this conversation:

[paste file content here]
```

Limitation: applies to the current conversation only. Must be repeated each session.

---

## Notes

- GPT Builder requires a ChatGPT Plus, Team, or Enterprise subscription.
- The model (GPT-4o, o1, etc.) is selected separately.
- Instructions count toward the context window of each conversation.
