---
name: setup-mcp
description: "Install and configure the mind-wiki MCP server for cross-directory and Claude Desktop access. Optional — only needed if you want to access your wiki from outside the wiki directory."
allowed-tools: Bash, Read, Write, Edit
---

# Mind Wiki MCP Setup

Install the MCP server so the wiki is accessible from any directory, any Claude Code project, and optionally Claude Desktop.

## Prerequisites

- Node.js 18+ must be installed
- The wiki must already be initialized (run "setup mind-wiki" first if not)

## Steps

1. Detect the current wiki path (the directory containing `wiki/index.md`). Store it as `WIKI_PATH`.

2. Ask the user: **"Do you also want to access your wiki from Claude Desktop? (yes/no)"**

3. Run the setup command based on their answer:

**Claude Code only:**
```bash
npx -y mind-wiki-mcp setup "WIKI_PATH"
```

**Claude Code + Claude Desktop:**
```bash
npx -y mind-wiki-mcp setup "WIKI_PATH" --claude-desktop
```

4. Verify the installation by checking that `~/.claude/settings.json` now contains the `mind-wiki` MCP server entry.

5. Confirm to the user:

```
Mind Wiki MCP server installed!

Your wiki is now accessible from:
  - Any Claude Code project (any directory)
  - Claude Desktop (if enabled)

Available tools:
  mind_query     Search your wiki
  mind_stats     Wiki dashboard  
  mind_lint      Health check
  mind_review    Review queue
  mind_index     Read the index
  mind_log       Activity log
  mind_init      Initialize wiki structure
  mind_pages     List/filter pages

To uninstall: npx mind-wiki-mcp uninstall
```

## Troubleshooting

If `npx` fails, try:
```bash
npm install -g mind-wiki-mcp
mind-wiki-mcp setup "WIKI_PATH"
```
