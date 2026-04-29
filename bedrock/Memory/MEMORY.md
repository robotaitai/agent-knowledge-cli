---
project: agent-knowledge
updated: 2026-04-29
---

# Memory

## Areas

- [packaging](packaging.md) -- PyPI: project-bedrock, CLI: bedrock (alias: agent-knowledge deprecated), v0.4.0, pipx install recommended
- [cli](cli.md) -- CLI, 24 subcommands, click framework; vault folder is ./bedrock/; migrate-vault added; refresh-system auto-renames cursor rule
- [architecture](architecture.md) -- Runtime modules, path resolution, project config v4; site.py: table rendering, graph neighbor highlight, wikilink edges
- [integrations](integrations.md) -- Multi-tool detection (Cursor/Claude/Codex), bridge files; cursor rule now bedrock.mdc; posix paths in JSON
- [testing](testing.md) -- 153 tests, pytest, GitHub Actions CI (ubuntu + macos, py 3.10/3.12/3.13)
- [stack](stack.md) -- Python 3.9+, Bash scripts, click, hatchling, no database/server
- [conventions](conventions.md) -- Naming rules, file layout conventions; update_when convention for durable-branch notes
- [deployments](deployments.md) -- CI pipeline, release process, PyPI publish
- [gotchas](gotchas.md) -- Known pitfalls; f-string backslash SyntaxError on Python < 3.12
- [history-layer](history-layer.md) -- Lightweight History/ diary, events.ndjson, backfill-history command
