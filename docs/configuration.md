# Configuration

Options go in the plugin's `options` in `opencode.json(c)`:

```jsonc
{
  "plugins": [
    {
      "package": "opencode-auto-retitle",
      "options": {
        "models": ["anthropic/claude-haiku-4-5", "openai/gpt-5.4-mini"],
        "onCompaction": true,
        "everyMessages": 15,
        "language": "auto",
        "maxWords": 6
      }
    }
  ]
}
```

| Option             | Default     | Description                                                                             |
| ------------------ | ----------- | --------------------------------------------------------------------------------------- |
| `models`           | none        | `provider/model`, or a list. The first that works is used, else the session's model.    |
| `onCompaction`     | `true`      | Check the title after each compaction.                                                  |
| `everyMessages`    | `0`         | Check the title every N of your messages. `0` turns it off.                             |
| `language`         | `"English"` | Language of the titles, or `"auto"`.                                                    |
| `maxWords`         | `6`         | Longest title, in words.                                                                |
| `firstMessages`    | `3`         | How many of your first messages the model reads, to see what the session is for.        |
| `recentMessages`   | `6`         | How many of the latest messages (yours and the agent's replies) the model reads.        |
| `languageMessages` | `5`         | With `language: "auto"`, how many of your latest messages decide the language.          |
| `pinManual`        | `true`      | Never change a title set with `/retitle <title>` automatically.                         |

A list of models lets one config work on machines with different providers. If none of them is available, or a call
fails, the check falls back to the model the session uses (without its variant), so titles still update when a
provider is down. Leave `models` out to always use the session's model.

## When the title is checked

- **After each compaction**, with the summary the compaction just wrote.
- **Every N messages**, once the turn has ended, so the model sees your message and the answer.

## What the model reads

Each check reads three parts of the session, and never tool calls or their output:

- **The start:** your first messages (`firstMessages`), which say what the session is for. The plugin keeps them as
  you send them, since compaction drops them from the session.
- **The summary** of the last compaction, if there is one.
- **The latest messages** (`recentMessages`): yours, and the text of the agent's replies. Your latest messages are also
  kept as you send them, so a check right after a compaction still sees them.

That is a few thousand tokens, so a check with a small model costs a fraction of a cent.

A title names the work as a whole, like a ticket or a branch: "Calendar migration to Vite", not "Vite build passes".
The model writes the title it would give the session, then keeps the current one if it names the same work and is a
good title. A new step, fix or release doesn't change it; a title like "Plugin is live" or "New session" is replaced.
Most checks change nothing. A session with no messages to go on keeps its title.

The message counter restarts after every check, compaction and rename.

## Language

With `"auto"`, the title follows the language of your latest messages (`languageMessages`, five by default). Code,
quotes and the agent's replies don't count. When your messages are mixed or too short to tell, the title keeps the
language it has.

Small models tend to write titles in English whatever they are asked, so the model writes the title in English and
names your language, and a second call translates it. Names of products and code stay as they are. A kept title in
another language is translated too, so a session that switches language gets its title in the new one.

## Commands

- `/retitle` writes a new title, from the same parts of the session as a check, and unpins the title.
- `/retitle <title>` sets the title. With `pinManual` on, automatic checks leave it alone until the next `/retitle`
  without a title.

A title you change in the OpenCode UI is not pinned, so a later check can replace it. Use `/retitle <title>` to keep it.
