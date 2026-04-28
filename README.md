<div align="center">

# agent-knowledge: Shared Memory for AI Development Teams

### Tell your AI developers how to work, and every session leaves the project smarter.

robotaitai

[![PyPI](https://img.shields.io/pypi/v/agent-knowledge-cli?color=blue&label=PyPI)](https://pypi.org/project/agent-knowledge-cli/)
[![Claude Code](https://img.shields.io/badge/Claude%20Code-ready-orange)](https://docs.anthropic.com/en/docs/claude-code)
[![Cursor](https://img.shields.io/badge/Cursor-ready-purple)](https://cursor.sh)
[![Codex](https://img.shields.io/badge/Codex-ready-black)](https://openai.com/codex)
[![License: MIT](https://img.shields.io/badge/License-MIT-green)](LICENSE)

<img src="docs/agent-knowledge-tour.gif" width="100%" alt="agent-knowledge tour" />

</div>

---

AI can write code fast.

What it does **not** do well by default is leave behind clear, shared project understanding.

Decisions disappear into chat history.  
Architecture gets rediscovered.  
New sessions start from zero.  
And the next developer, human or AI, has to figure out again what changed, where, and why.

**agent-knowledge** gives every repo a shared memory layer for humans and AI developers.

It works like the operating discipline of a strong team lead:
- every session starts with context
- every important change leaves a trail
- stable knowledge gets written down where the next developer can find it
- the project becomes easier to understand over time, not harder

With one command, your project gets:
- structured memory for architecture, decisions, conventions, and history
- project-local integration for **Claude Code** and **Cursor**
- lightweight git-friendly markdown that lives with the repo
- HTML, graph, and Obsidian-ready views of what the project knows

Under the hood, it is just markdown files and a CLI.  
No database. No server. No hosted backend. No black box.

**The result:** your AI developers stop behaving like disconnected sessions, and start behaving more like a team.

## Install

```bash
pip install agent-knowledge-cli
```

PyPI package name: `agent-knowledge-cli`. CLI command and all docs: `agent-knowledge`.

## Quick Start

```bash
cd your-project
agent-knowledge init
```

That's it. Open the project in Claude Code or Cursor and the agent has
persistent memory automatically -- no manual prompting, no config, no setup.

`init` does everything in one shot:
- creates `./agent-knowledge/` as a real directory inside the repo (git-tracked)
- registers the project in `~/agent-os/projects/<slug>/` as a symlink back into the repo, so **every project on your machine shows up in one place** -- open `~/agent-os/projects/` in Obsidian and you have a single vault that spans all your teams' logbooks
- adds noisy subfolders (`Evidence/raw/`, `Outputs/site/`, ...) to `.gitignore` automatically
- installs project-local integration for both Claude Code and Cursor
- detects Codex and installs its bridge files if present
- bootstraps the memory tree and marks onboarding as `pending`
- imports repo history into `Evidence/` and backfills lightweight history from git

## Storage modes

By default, knowledge lives **inside** the repo at `./agent-knowledge/` (git-tracked) and
`~/agent-os/projects/<slug>/` is a symlink back to it. Curated knowledge (`Memory/`,
`History/`, `Evidence/imports/`) is committed normally; noisy subfolders are gitignored.

To keep knowledge **outside** the repo instead:

```bash
agent-knowledge init --external
```

In external mode:
- Knowledge lives in `~/agent-os/projects/<slug>/` (not committed)
- `./agent-knowledge` is a symlink into that folder

To convert an existing external-vault project to in-repo:

```bash
agent-knowledge migrate-to-local
```

## How It Works

Think of the vault as your team's shared notebook. It has a few clearly
separated sections so a casual scribble doesn't get mistaken for a confirmed
fact, and so the manager (you) can tell at a glance what is canon vs. what is
just chatter.

### Knowledge layers

| Folder | Role | Who writes here | Canonical? |
|--------|------|-----------------|-----------|
| `Memory/` | Decisions, conventions, architecture, gotchas -- the things you'd tell a new hire | Developer (deliberately, at session end) | Yes |
| `History/` | What happened, and when -- a dated trail of releases and milestones | The CLI (auto, from git) | Yes |
| `Evidence/` | Raw imports: docs, ADRs, PRs, screenshots, anything captured for context | Auto-imported on sync | No |
| `Outputs/` | Generated views: HTML site, search index, knowledge map | The CLI (auto) | No |

The discipline that makes this work: **only `Memory/` and `History/` are canon.**
Nothing imported, captured, or generated is ever treated as truth on its own.
A developer (or AI agent) has to consciously promote something into `Memory/`
for it to count -- which is exactly what you'd want from any team member
writing to a shared logbook.

## Project-local integration

The project carries everything it needs. Both Claude Code and Cursor get full
integration installed automatically -- hooks, runtime contracts, and slash commands.
No global config required.

## Obsidian-ready -- one vault for every project

Each project's `./agent-knowledge/` is a valid Obsidian vault on its own. But
the real payoff is `~/agent-os/projects/`: every project you've ever run
`init` in is registered there as a symlink. Open that single folder in
Obsidian and you have **a unified vault across all your teams' projects** --
with backlinks, graph view, and full-text search spanning every codebase you
manage. No per-project Obsidian setup. No re-opening windows when you switch
contexts. One window, every team.

For a spatial canvas of the knowledge graph:

```bash
agent-knowledge export-canvas
# produces: agent-knowledge/Outputs/knowledge-export.canvas
```

The vault is designed to work well in Obsidian -- good markdown, YAML frontmatter,
branch-note convention, internal links. But everything works without it too.

### What gets installed

**Claude Code** (`.claude/`):

| File | Purpose |
|------|---------|
| `settings.json` | Lifecycle hooks: sync on SessionStart, Stop, PreCompact |
| `CLAUDE.md` | Runtime contract: knowledge layers, session protocol, onboarding |
| `commands/memory-update.md` | `/memory-update` slash command |
| `commands/system-update.md` | `/system-update` slash command |
| `commands/absorb.md` | `/absorb <file/folder>` slash command |

**Cursor** (`.cursor/`):

| File | Purpose |
|------|---------|
| `rules/agent-knowledge.mdc` | Always-on rule: loads memory context on every session |
| `hooks.json` | Lifecycle hooks: sync on start, update on write, sync on stop/compact |
| `commands/memory-update.md` | `/memory-update` slash command |
| `commands/system-update.md` | `/system-update` slash command |
| `commands/absorb.md` | `/absorb <file/folder>` slash command |

**Codex** (`.codex/`) -- installed when detected:

| File | Purpose |
|------|---------|
| `AGENTS.md` | Agent contract with knowledge layer instructions |

### Session lifecycle

Hooks fire automatically -- the agent syncs memory at the start of every session
and captures state at the end, with no manual intervention:

| Event | Claude Code | Cursor | What runs |
|-------|-------------|--------|-----------|
| Session start | SessionStart | session-start | `agent-knowledge sync` |
| File saved | -- | post-write | `agent-knowledge update` |
| Task complete | Stop | stop | `agent-knowledge sync` |
| Context compaction | PreCompact | preCompact | `agent-knowledge sync` |

The runtime contract ensures the agent reads `STATUS.md` and `Memory/MEMORY.md`
at the start of every session, with no manual prompting required.

### Slash commands

These are how the team writes to the logbook. Both work in any Claude Code
or Cursor session and are project-local -- `init` installed them.

| Command | Who runs it | When | What it does |
|---------|-------------|------|-------------|
| `/memory-update` | The developer at the end of a session | Before logging off, before opening a PR | Syncs state, then the agent reviews what happened this session, writes confirmed stable facts into `Memory/`, and summarizes what changed. **This is the team handoff** -- whoever (or whatever) opens the project next will read it. |
| `/system-update` | You, the manager | After upgrading `agent-knowledge-cli` | Refreshes the framework's plumbing: hooks, rules, commands, AGENTS.md. Has nothing to do with the project's knowledge content -- purely infrastructure. |

The intent: a developer should never finish a session without running
`/memory-update`. It is the equivalent of a daily standup writeup -- short,
factual, and the next person on the team gets it for free.

### Integration health

```bash
agent-knowledge doctor
```

Reports whether all integration files (settings, hooks, rules, commands) are
installed and current for both Claude Code and Cursor. If anything is stale or
missing, `doctor` tells you exactly what to run.

## Commands

| Command | What it does |
|---------|-------------|
| `init` | Set up a project -- knowledge in-repo by default, one command, no arguments |
| `sync` | Full sync: memory, history, git evidence, index |
| `ship` | Validate + sync + commit + push |
| `view` | Build site and open in browser |
| `doctor` | Validate setup, integration health, note staleness |

Other commands: `absorb`, `search`, `export-html`, `clean-import`, `refresh-system`, `backfill-history`, `compact`, `migrate-to-local`, `init --external`. Run `agent-knowledge --help` for the full list.

All write commands support `--dry-run` and `--json`.

## More

- [Static site export](docs/reference.md#static-site-export) -- `agent-knowledge view` builds an interactive HTML site from your vault
- [Automatic capture](docs/reference.md#automatic-capture) -- every sync event is recorded as lightweight evidence
- [Progressive retrieval](docs/reference.md#progressive-retrieval) -- agents load only the branches they need
- [Clean web import](docs/reference.md#clean-web-import) -- import a URL as cleaned markdown evidence
- [Project history](docs/reference.md#project-history) -- lightweight event log auto-backfilled from git
- [Keeping up to date](docs/reference.md#keeping-up-to-date) -- `pip install -U` + `refresh-system`
- [Custom knowledge home](docs/reference.md#custom-knowledge-home) -- change where `~/agent-os/` lives
- [Troubleshooting](docs/reference.md#troubleshooting) -- common issues and fixes
- [Platform support](docs/reference.md#platform-support) -- macOS, Linux, Python 3.9+
- [Development](docs/reference.md#development) -- contributing and running tests

## Star History

[![Star History Chart](https://api.star-history.com/svg?repos=robotaitai/agent-knowledge-cli&type=Date)](https://star-history.com/#robotaitai/agent-knowledge-cli&Date)
