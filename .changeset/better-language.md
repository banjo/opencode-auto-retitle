---
"opencode-auto-retitle": patch
---

Better titles with `language: "auto"`:

- Titles come out in your language. The model writes the title in English and names your language, and a second call
  translates it. A kept title in another language is translated too.
- Your latest messages are kept as you send them, so the language and the title still follow them right after a
  compaction.
- A check writes the title it would give the session first, then keeps the current one only if it names the same work
  and is a good title. Titles like "Plugin is live" are replaced.
- Placeholder titles such as "New session - …" are always replaced.
