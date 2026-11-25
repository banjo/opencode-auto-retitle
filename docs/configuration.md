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

| Option          | Default     | Description                                                                                  |
| --------------- | ----------- | -------------------------------------------------------------------------------------------- |
| `models`        | none        | Required. `provider/model`, or a list of them. The first one that is available is used.     |
| `onCompaction`  | `true`      | Check the title after each compaction.                                                       |
| `everyMessages` | `0`         | Check the title every N of your messages. `0` turns it off.                                  |
| `language`      | `"English"` | Language of the titles, or `"auto"`.                                                         |
| `maxWords`      | `6`         | Longest title, in words.                                                                     |
| `recentChars`   | `20000`     | How much of the recent conversation the model reads, in characters.                         |
| `pinManual`     | `true`      | Never change a title set with `/retitle <title>` automatically.                             |

A list of models lets one config work on machines with different providers.

## When the title is checked

- **After compaction**, the model reads the compaction summary.
- **Every N messages**, the check runs once the turn has ended, so the model reads your message and the answer. It
  reads the messages since the last compaction, up to `recentChars`. When they don't fit, it keeps the compaction
  summary and the first message, which say what the session is for, and the newest messages. Tool output is left out.

A title names the work as a whole, like a ticket or a branch: "Calendar migration to Vite", not "Vite build passes".
The model replies `KEEP` unless the session has moved on to different work, so most checks change nothing. A session
with no messages to go on keeps its title.

The message counter restarts after every check, compaction and rename.

## Language

With `"auto"`, the title follows the language of your three latest messages. Code, quotes and the agent's replies don't
count. When your messages are mixed or too short to tell, the title keeps the language it has.

## Commands

- `/retitle` writes a new title from the recent messages, and unpins the title.
- `/retitle <title>` sets the title. With `pinManual` on, automatic checks leave it alone until the next `/retitle`
  without a title.

A title you change in the OpenCode UI is not pinned, so a later check can replace it. Use `/retitle <title>` to keep it.
