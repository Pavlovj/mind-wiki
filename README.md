# mind-wiki

A typed, compounding personal knowledge wiki skill for AI coding agents. Your agent maintains a structured wiki that grows smarter with every interaction — knowledge compounds instead of evaporating into chat history.

Works seamlessly with [Obsidian](https://obsidian.md) — wikilinks, graph view, Dataview queries, and tags all work out of the box.

## Install

```bash
npx add-skill pavlovj/mind-wiki
```

## Quick Start

```
"setup mind-wiki"                    → Initialize wiki structure
"Dump this article about RSC"        → Ingest a source
"What do I know about auth?"         → Query your knowledge
"Lint my wiki"                       → Health check
"Wiki stats"                         → Dashboard
"Review my wiki"                     → Walk through unverified pages
"setup mind-wiki mcp"                → (Optional) Access from any directory / Claude Desktop
```

## How It Works

**Five operations:**

| Operation | What it does |
|-----------|-------------|
| **Ingest** | Classifies source type → extracts with appropriate depth → creates typed wiki pages → extracts entities → cross-references everything |
| **Query** | Searches index first (not full pages) → synthesizes cited answers → updates wiki with new knowledge surfaced |
| **Lint** | Auto-fixes broken links, missing frontmatter, stale dates. Reports contradictions, orphans, tag inconsistencies |
| **Review** | Guided walkthrough of `needs_review: true` pages, sorted by confidence. Approve, edit, or skip |
| **Stats** | Page counts, domain distribution, top tags, health warnings |

**Seven entity types** — each with its own template:

| Type | Use For |
|------|---------|
| Idea | Thoughts, brainstorms |
| Journal | Diary entries, reflections |
| Person | People you interact with |
| Strategy | Plans, playbooks |
| Technology | Tools, frameworks, patterns |
| Project | Things you're building |
| Resource | Articles, books, videos, repos |

**Key patterns:**
- **Classify before extract** — a 2-line idea gets different treatment than a 10-page article
- **L0→L3 progressive disclosure** — token budgets prevent context waste
- **Dual output** — every task produces an answer AND wiki updates
- **Cross-domain tagging** — `domains:` (broad) + `tags:` (granular) on every page
- **Source citations + `needs_review` flags** — you verify, the agent writes

**MCP server** (optional) — install with `setup mind-wiki mcp` to access your wiki from any Claude Code project or Claude Desktop via [mind-wiki-mcp](https://github.com/pavlovj/mind-wiki-mcp).

## Compatibility

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code)
- [Cursor](https://cursor.sh)
- [Codex CLI](https://github.com/openai/codex)
- Any tool supporting the [agentskills.io](https://agentskills.io) standard

## Inspired By

- [Andrej Karpathy's LLM Wiki](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f) — the foundational pattern
- Production community tips — classify-before-extract, token budgets, typed templates, dual output rule, cross-domain design

## License

MIT
