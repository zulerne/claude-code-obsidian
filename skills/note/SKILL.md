---
name: note
description: Create an Obsidian note summarizing the current session
argument-hint: "[learn|log|all]"
disable-model-invocation: true
allowed-tools:
  - Read
  - Write
  - Glob
---

## Git context

- Recent commits: !`git log --oneline -5 2>/dev/null || echo "not a git repo"`
- Changed files: !`git diff --stat 2>/dev/null || echo "no changes"`

## Configuration

The user's CLAUDE.md (already in context) contains an `claude-code-obsidian-plugin-config` section with a YAML block. Extract these values:

- `obsidian_vault` (required) — absolute path to the vault root
- `learn_dir` (optional, default: `Learn`) — subdirectory for learn notes
- `logs_dir` (optional, default: `Logs`) — subdirectory for session logs
- `note_language` (optional, default: `English`) — language for note content (code, commands, filenames always stay in English)

If `obsidian_vault` is not found in context, stop and tell the user:
> To use /note, add Obsidian config to your `~/.claude/CLAUDE.md`. See: https://github.com/zulerne/claude-code-obsidian#configuration

## Task

Analyze the current conversation and create an Obsidian note.

### Step 1: Determine note types

If the user passed an argument (`$ARGUMENTS`), use it directly:
- `learn` — create only learn note(s)
- `log` — create only a session log
- `all` — create both learn note(s) and a log (same as default smart behavior, but explicit)

Otherwise, analyze the conversation and identify **all** applicable types:
- If the session involved learning, explaining, or discussing fundamental concepts → plan learn note(s)
- If the session involved project work: writing code, fixing bugs, configuring tools, refactoring → plan a log
- If both → plan both

Multiple learn notes are allowed if the session covered **distinct topics** (e.g., Docker networking and Git rebase → two separate learn notes). Do not split into multiple notes if topics are closely related.

### Step 2: Create the note

#### For Learn notes

Location: `<obsidian_vault>/<learn_dir>/<section>/<Topic>.md`

Use the values from the config. Choose `<section>` based on the topic (e.g., `Docker/`, `Git/`, `AWS/`).

Structure:

```
# Topic Name

Concise description of what this is.

## Why

- When and why to use this

## <Main content sections>

Core knowledge from the session. Dense, technical, textbook-style.
Use code blocks, tables, callouts as appropriate.

## Links

- [Docs](url)
- [[wikilink|Related Note]] — if relevant notes exist in the vault

#tool #topic-tag
```

#### For session logs

Location: `<obsidian_vault>/<logs_dir>/YYYY-MM-DD-short-description.md`

Use lowercase Latin for the filename slug (e.g., `2026-02-16-fix-obsidian-notes.md`).

Structure:

```
# Short session description

Date: YYYY-MM-DD
Project: project name or directory

## What was done

- Bullet list of completed work

## Key decisions

- Important decisions and why they were made
(omit this section if there were no notable decisions)

## Changed files

- `path/to/file.md` — brief description of change
(omit this section if no files were changed)

#log #topic-tag
```

**Note on language:** The templates above are in English (the default). If `note_language` is set to something else (e.g., `Russian`), translate the section headers and prose into that language. Code, commands, filenames, and tags always stay in English.

### Step 3: Write the notes

For each planned note:

1. Use Glob to check the target path does not already exist
2. Write the note using the Write tool

After all notes are written, report all created files and their paths.

### Rules

- No emojis
- Use Obsidian-flavored Markdown: `> [!INFO]`, `> [!tip]` callouts, `[[wikilinks]]`
- Tags at the end of file: `#tool`/`#log` + topic tags (`#go`, `#docker`, `#claude-code`, etc.)
- Be concise and technical. Capture the essence, not the full conversation.
- If there is nothing meaningful to note from the session, say so instead of creating an empty note.
