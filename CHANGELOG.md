# opencode-auto-retitle

## 0.1.1

### Patch Changes

- Titles name the work as a whole, like a ticket or a branch, instead of the latest step or event.
- Each check reads how the session started (your first messages, kept even after compaction), the last compaction
  summary and the latest messages, without tool calls. New options: `firstMessages` (3), `recentMessages` (6) and
  `languageMessages` (5). `recentChars` is gone.
- With `language: "auto"`, your five latest messages decide the language.
- A session with no messages to go on keeps its title.
