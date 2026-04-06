---
name: setup
description: "Initialize the mind-wiki directory structure. Creates sources/, wiki/, skills/ and all page type subdirectories. Safe to run multiple times."
allowed-tools: Bash, Read, Write
---

# Mind Wiki Setup

Initialize the wiki structure in the current directory. Only creates what's missing — safe to run multiple times.

## Steps

1. Create the directory structure:

```bash
mkdir -p sources wiki/pages/ideas wiki/pages/journal wiki/pages/people wiki/pages/strategies wiki/pages/technology wiki/pages/projects wiki/pages/resources skills
```

2. Create `wiki/index.md` if it doesn't exist (read `references/index-template.md` from the skill directory and write it to `wiki/index.md`).

3. Create `wiki/log.md` if it doesn't exist (read `references/log-template.md` from the skill directory and write it to `wiki/log.md`). Add an initialization entry with today's date.

4. Confirm to the user:

```
Mind Wiki initialized!

Structure:
  sources/           ← Drop raw material here
  wiki/
    index.md         ← Page catalog (L1)
    log.md           ← Activity log
    pages/
      ideas/         journal/       people/
      strategies/    technology/    projects/
      resources/
  skills/            ← Reusable patterns

Ready to go. Try: "Ingest this: <paste anything>"
```

5. Do NOT install the MCP server. If the user wants remote access from other directories or Claude Desktop, tell them to run: **"setup mind-wiki mcp"**
