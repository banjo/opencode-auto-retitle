# opencode-auto-retitle

## 0.1.3

### Patch Changes

- 677d8c4: Falls back to the session's own model when none of `models` is available or a call fails, so titles still update when
  a provider is down. `models` is no longer required.

## 0.1.2

### Patch Changes

- 2939508: Better titles with `language: "auto"`:
  
  - Titles come out in your language. The model writes the title in English and names your language, and a second call
    translates it. A kept title in another language is translated too.
  - Your latest messages are kept as you send them, so the language and the title still follow them right after a
    compaction.
  - A check writes the title it would give the session first, then keeps the current one only if it names the same work
    and is a good title. Titles like "Plugin is live" are replaced.
  - Placeholder titles such as "New session - …" are always replaced.

## 0.1.1

### Patch Changes

- Titles name the work as a whole, like a ticket or a branch, instead of the latest step or event.
- Each check reads how the session started (your first messages, kept even after compaction), the last compaction
  summary and the latest messages, without tool calls. New options: `firstMessages` (3), `recentMessages` (6) and
  `languageMessages` (5). `recentChars` is gone.
- With `language: "auto"`, your five latest messages decide the language.
- A session with no messages to go on keeps its title.
