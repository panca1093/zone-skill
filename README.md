# zone-skill

A spec-driven development pipeline as a custom slash command for AI coding tools. One command takes an idea from brief to shipped PR — no manual handoffs.

Inspired by Kuroko no Basket — entering the Zone is peak performance state where everything flows.

## Pipeline

```
/zone  →  brief  →  spec  →  plan  →  implement (TDD loop)  →  review  →  test  →  ship
```

Each step advances automatically. State is tracked in `.zone/manifest.json` at the repo root. Re-run `/zone` after each step to continue.

## Prerequisites

- [Claude Code](https://claude.ai/code) or [OpenCode](https://opencode.ai)
- `gh` CLI (for PR creation in the ship step)
- Git
- A Jira MCP server (optional — only needed for the Jira path)
- A Notion MCP server (optional — only needed if you want Notion sync)

## Install

```bash
git clone git@github.com:panca1093/zone-skill.git
cd zone-skill
bash install.sh
```

The installer:
1. Detects which platforms are installed (Claude Code, OpenCode)
2. Prompts for Notion database/page IDs (press Enter to skip)
3. Writes Notion IDs to the platform's native config (`~/.claude/settings.json` env block for Claude Code; shell profile for OpenCode)
4. Copies skill files to the platform's commands directory

## Usage

```bash
/zone TICKET-123        # Jira path — fetches ticket, creates spec + tasks in Notion
/zone                   # Scratch path — interactive brief, personal Notion workspace
/zone TICKET-123 --no-notion  # skip Notion for this session
```

### Jira path

Pass a ticket ID. Zone fetches the ticket, asks clarifying questions, writes a spec, breaks it into TDD tasks, implements them one by one, self-reviews, runs tests, then opens a PR.

### Scratch path

No ticket needed. Zone opens a brainstorm conversation to sharpen the idea before moving into spec and implementation.

## Notion sync (optional)

When configured, Zone creates a spec page and task rows in Notion and keeps them in sync throughout the pipeline. Four env vars are required:

| Variable | Purpose |
|---|---|
| `ZONE_NOTION_WORK_DB_ID` | Tasks database for work projects |
| `ZONE_NOTION_PERSONAL_DB_ID` | Tasks database for personal projects |
| `ZONE_NOTION_WORK_PARENT_ID` | Parent page for work specs |
| `ZONE_NOTION_PERSONAL_PARENT_ID` | Parent page for personal specs |

Run `bash install.sh` to set these interactively, or set them manually in your platform's config.

## Supported platforms

| Platform | Commands dir | Notion config |
|---|---|---|
| Claude Code | `~/.claude/commands/` | `~/.claude/settings.json` env block |
| OpenCode | `~/.config/opencode/commands/` | shell profile (`~/.zshrc`) |

Adding a new platform: update `install.sh` with the platform's commands directory and env var injection mechanism.

## Language support

The implement and test steps auto-detect the project type:

| Language | Detection | Test command |
|---|---|---|
| Go | `go.mod` | `go test ./... -race` |
| Node/JS | `package.json` | `npm test` / `yarn test` |
| Python | `pyproject.toml` / `requirements.txt` | `pytest` |
| Rust | `Cargo.toml` | `cargo test` |
| Other | `Makefile` test target | `make test` |

## Project-level conventions

Architectural rules (layering, naming, etc.) belong in your project's `CLAUDE.md` or `AGENTS.md`. Zone reads these files during the implement step and applies them automatically.
