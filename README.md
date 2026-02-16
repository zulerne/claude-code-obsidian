# claude-code-obsidian

[![Claude Code Skill](https://img.shields.io/badge/Claude-Code_Skill-violet)](https://claude.ai/code)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

Claude Code skills for creating [Obsidian](https://obsidian.md/) notes from your coding sessions.

Ever spent the whole day coding with Claude — debugging, learning new tools, refactoring — and by evening you can't recall half of what you did? Or explained something to yourself during a session, understood it perfectly, and then forgot the details a week later?

`/note` fixes that. One command at the end of a session, and you have a clean note in your vault.

## What it does

The `/note` skill analyzes your current Claude Code session and creates a well-structured Obsidian note:

- **Learn notes** — when you explored a concept, learned something new, or discussed how things work. Creates a dense, textbook-style reference note.
- **Session logs** — when you wrote code, fixed bugs, or configured tools. Creates a concise log of what was done, key decisions, and changed files.

The skill automatically picks the right note type based on the session content, or you can force it with `/note learn` or `/note log`.

## Quick start

1. Copy the skill:

```bash
cp -r skills/note ~/.claude/skills/note
```

2. Add config to your `~/.claude/CLAUDE.md`:

```markdown
## obsidian-skills config

​```yaml
obsidian_vault: /Users/yourname/Documents/obsidian/my-vault
​```
```

3. Run `/note` at the end of a session. That's it.

### Try it now

Open Claude Code in any project and have a conversation — ask it to explain something or fix a bug. Then type:

```
/note
```

Claude will analyze the conversation and create a note in your vault.

## Configuration

Add an `obsidian-skills config` section to your `~/.claude/CLAUDE.md` with a YAML block:

```yaml
obsidian_vault: /Users/yourname/Documents/obsidian/my-vault
learn_dir: Learn
logs_dir: Logs
note_language: English
```

| Key | Required | Default | Description |
|---|---|---|---|
| `obsidian_vault` | Yes | — | Absolute path to your Obsidian vault |
| `learn_dir` | No | `Learn` | Subdirectory for learn notes |
| `logs_dir` | No | `Logs` | Subdirectory for session logs |
| `note_language` | No | `English` | Language for note content (code/commands stay in English) |

## Usage

```
/note          # auto-detect note type from conversation
/note learn    # force a learn note
/note log      # force a session log
```

### Examples

After a session learning about Docker networking:

```
/note
→ Created: ~/vault/Learn/Docker/Container Networking.md
```

After a session fixing a CI pipeline:

```
/note log
→ Created: ~/vault/Logs/2026-02-16-fix-ci-pipeline.md
```

## Note structure

### Learn notes

```
vault/
  Learn/
    Docker/
      Container Networking.md
    Git/
      Interactive Rebase.md
```

Each learn note includes: concept description, when/why to use it, core technical content, and links to docs or related vault notes.

### Session logs

```
vault/
  Logs/
    2026-02-16-fix-ci-pipeline.md
    2026-02-15-add-auth-middleware.md
```

Each session log includes: date, project name, what was done, key decisions, and changed files.

## Tips

- Run `/note` **before** `/compact` or `/clear` — the skill needs the full conversation to produce a good note.
- No point running `/note` on a fresh or near-empty session. The skill will refuse to create an empty note.
- For long sessions with multiple topics, run `/note` at natural breakpoints rather than once at the very end.

## Requirements

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) CLI
- An Obsidian vault (or any folder — notes are plain Markdown)

## License

MIT
