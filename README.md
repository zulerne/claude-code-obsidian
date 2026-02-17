# claude-code-obsidian

[![Claude Code Skill](https://img.shields.io/badge/Claude-Code_Skill-violet)](https://claude.ai/code)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

Claude Code skills for creating [Obsidian](https://obsidian.md/) notes from your coding sessions.

Ever spent the whole day coding with Claude — debugging, learning new tools, refactoring — and by evening you can't recall half of what you did? Or explained something to yourself during a session, understood it perfectly, and then forgot the details a week later?

`/note` fixes that. One command at the end of a session, and you have a clean note in your vault.

## What it does

The `/note` skill analyzes your current Claude Code session and creates well-structured Obsidian notes:

- **Learn notes** — when you explored a concept, learned something new, or discussed how things work. Creates a dense, textbook-style reference note.
- **Session logs** — when you wrote code, fixed bugs, or configured tools. Creates a concise log of what was done, key decisions, and changed files.

The skill automatically detects what happened in the session and creates the appropriate notes. Mixed sessions (learning + practical work) produce multiple notes — learn note(s) for what was learned and a log for what was done. If the session covered distinct topics, separate learn notes are created for each.

## Installation

### Option A: As a plugin

```
/plugin marketplace add zulerne/claude-code-obsidian
/plugin install obsidian@zulerne-claude-code-obsidian
```

### Option B: As a standalone skill

```bash
git clone https://github.com/zulerne/claude-code-obsidian.git
cp -r claude-code-obsidian/skills/note ~/.claude/skills/note
```

Restart Claude Code after installing.

### Configure

Add config to your `~/.claude/CLAUDE.md` (required for both options):

```markdown
## claude-code-obsidian-plugin-config

​```yaml
obsidian_vault: /Users/yourname/Documents/obsidian/my-vault
learn_dir: Learn       # subdirectory for learn notes
logs_dir: Logs         # subdirectory for session logs
note_language: English # language for note content
​```
```

### Try it now

Open Claude Code in any project and have a conversation — ask it to explain something or fix a bug. Then type:

```
/note
```

Claude will analyze the conversation and create a note in your vault.

## Configuration

Add this block to your `~/.claude/CLAUDE.md`:

```markdown
## claude-code-obsidian-plugin-config

​```yaml
obsidian_vault: /Users/yourname/Documents/obsidian/my-vault
learn_dir: Learn       # subdirectory for learn notes
logs_dir: Logs         # subdirectory for session logs
note_language: English # language for note content
​```
```

| Key | Required | Default | Description |
|---|---|---|---|
| `obsidian_vault` | Yes | — | Absolute path to your Obsidian vault |
| `learn_dir` | No | `Learn` | Subdirectory for learn notes |
| `logs_dir` | No | `Logs` | Subdirectory for session logs |
| `note_language` | No | `English` | Language for note content (code/commands stay in English) |

## Usage

```
/note          # auto-detect: creates all applicable note types
/note learn    # only learn note(s)
/note log      # only a session log
/note all      # explicitly create both (same as default smart behavior)
```

### Examples

After a session learning about Docker networking:

```
/note
→ Created: ~/vault/Learn/Docker/Container Networking.md
```

After a mixed session — learned about Docker networking while fixing a CI pipeline:

```
/note
→ Created: ~/vault/Learn/Docker/Container Networking.md
→ Created: ~/vault/Logs/2026-02-16-fix-ci-pipeline.md
```

After a session fixing a CI pipeline (log only):

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

## How it works

When you invoke `/note`, the skill:

1. Runs `git log --oneline -5` and `git diff --stat` to gather current repo context (read-only, fails silently outside git repos)
2. Reads your config from `~/.claude/CLAUDE.md` (already in context — no extra tool calls)
3. Analyzes the conversation to determine which note types apply (learn, log, or both)
4. Writes one or more Markdown files to your vault using the `Write` tool

The skill only has access to `Read`, `Write`, and `Glob` tools.

## Tips

- Run `/note` **before** `/compact` or `/clear` — the skill needs the full conversation to produce a good note.
- No point running `/note` on a fresh or near-empty session. The skill will refuse to create an empty note.
- Mixed sessions are handled automatically — `/note` will create both learn and log notes as needed. For very long sessions, you can still run `/note` at natural breakpoints.

## Requirements

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) CLI
- An Obsidian vault (or any folder — notes are plain Markdown)

## License

MIT
