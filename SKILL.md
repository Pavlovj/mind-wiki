---
name: mind-wiki
description: "Use when building or maintaining a typed personal knowledge wiki. Triggers: ingesting sources, querying knowledge, linting wiki quality, reviewing pages, wiki stats, 'add to wiki', 'what do I know about', 'ingest this', 'dump this', 'wiki', 'second mind', 'knowledge base', 'mind-wiki'."
allowed-tools: Read, Write, Edit, Glob, Grep, Bash, WebFetch
---

# Mind Wiki

You are maintaining a typed, compounding personal knowledge wiki. Knowledge is compiled once and kept current — not re-derived every conversation. You are the writer. The human is the editor-in-chief.

This wiki is fully compatible with [Obsidian](https://obsidian.md). All `[[wikilinks]]` render as navigable links, frontmatter is queryable via the Dataview plugin, and the cross-reference graph is visualizable in Obsidian's graph view.

This skill handles five operations: **Ingest**, **Query**, **Lint**, **Review**, and **Stats**.

Two setup commands are available:
- **`setup mind-wiki`** — Initialize the wiki directory structure
- **`setup mind-wiki mcp`** — (Optional) Install the MCP server for cross-directory and Claude Desktop access

---

## Directory Structure

```
sources/            ← Raw source material (immutable, write-once)
  <topic>/
    YYYY-MM-DD-slug.md

wiki/               ← LLM-maintained knowledge layer
  index.md          ← L1 catalog — always read this first
  log.md            ← Append-only activity log
  pages/
    ideas/          ← Idea pages
    journal/        ← Journal/diary entries
    people/         ← Person pages
    strategies/     ← Strategy/plan pages
    technology/     ← Technology/tool/pattern pages
    projects/       ← Project pages
    resources/      ← Article/book/video summaries

skills/             ← Reusable skills, commands, patterns
```

### Initialization

The wiki structure is created on first Ingest. Create only what is missing — never overwrite existing files. If a user tries to Query or Lint before any Ingest, respond: **"Run an ingest first to initialize the wiki."**

Required on first init:
- `sources/`
- `wiki/`, `wiki/pages/` and all 7 subdirectories
- `wiki/index.md` (from `references/index-template.md`)
- `wiki/log.md` (from `references/log-template.md`)
- `skills/`

---

## Progressive Disclosure (Token Budget)

ALWAYS follow this reading discipline:

| Level | ~Tokens | What | When |
|-------|---------|------|------|
| L0 | ~200 | This skill's core rules | Every session |
| L1 | ~1-2K | `wiki/index.md` | Before ANY query or ingest |
| L2 | ~2-5K | Relevant page summaries | When answering questions |
| L3 | 5-20K | Full page content | Only when L2 is insufficient |

**Rule: Never jump to L3.** Check the index (L1) first. Read summaries (L2) second. Only read full pages when you genuinely need the detail.

---

## Operation 1: Ingest

**Triggers:** "Ingest this", "Add to wiki", "Dump this", providing a URL/file/pasted text, or any new information the user shares.

### Phase 1 — Fetch

Retrieve the content via web fetch, file read, or user paste.

**Batch ingest:** If the user says "ingest everything in this folder" or provides multiple sources, process each source sequentially through the full pipeline. Log each individually but provide a single summary at the end listing all pages created/updated.

### Phase 2 — Classify

Determine source type BEFORE extracting. Classification determines extraction depth and target page type:

| Source Type | Extraction Depth | Wiki Page Type |
|-------------|-----------------|----------------|
| Quick thought (< 3 sentences) | Minimal — capture as-is | `idea` (seed status) |
| Idea / brainstorm | Core thesis, implications, open questions | `idea` |
| Article / reference material | Full summary, key claims, entity extraction | `resource` |
| Journal / reflection / personal | Mood, themes, decisions, open loops | `journal` |
| Conversation transcript | Decisions, action items, key insights | Extract to relevant types |
| Strategy / plan | Goals, steps, assumptions, risks, timeline | `strategy` |
| Technical knowledge | Concepts, patterns, code examples, gotchas | `technology` |
| Person info | Role, context, relationship | `person` |
| Project info | Overview, goals, architecture, status | `project` |

A single source may produce multiple wiki pages (e.g., an article about a person using a technology → `resource` + `person` + `technology` pages).

### Phase 3 — Save Source

Save the raw source to `sources/<topic>/YYYY-MM-DD-descriptive-slug.md` using the format in `references/source-template.md`.

Sources are **immutable** — never modify after creation.

### Phase 4 — Compile Wiki Pages

For each wiki page to create or update:

1. **Check if a page for this entity/concept already exists** — search `wiki/index.md` first
2. **If exists:** Update the existing page with new information. Refresh the `updated:` date. If new info contradicts existing content, annotate the disagreement with source attribution — never silently overwrite
3. **If new:** Create a new page using the appropriate type template from `references/`. Fill the frontmatter completely:
   - `domains:` — tag ALL relevant domains (e.g., `[personal, tech, business]`)
   - `tags:` — add granular free-form tags (e.g., `[saas, productivity, mvp]`)
   - `sources:` — cite the source file path (e.g., `sources/ai/2026-04-06-llm-patterns.md`)
   - `confidence:` — `high` (verified facts), `medium` (reasonable synthesis), `low` (speculation)
   - `needs_review: true` — always true for LLM-created content
   - `related:` — explicit links to related pages (queryable, unlike body wikilinks)
   - `created:` and `updated:` — today's date
4. **Extract entities** — people, companies, technologies, projects mentioned. Create or update their pages too
5. **Add cross-references** — use `[[page-name]]` wikilinks in the body to connect related pages, AND add paths to `related:` in frontmatter

### Phase 5 — Update Index & Log

1. **Update `wiki/index.md`** — add new entries or update summaries under the correct type heading. Format per entry:
   ```
   - [Page Title](pages/<type>/filename.md) — one-line summary (domains: x, y)
   ```
2. **Append to `wiki/log.md`:**
   ```
   ## [YYYY-MM-DD] ingest | {Source Title}
   - Source: `sources/<topic>/filename.md`
   - Created: {list of new pages}
   - Updated: {list of updated pages}
   - Entities extracted: {list}
   ```

### Phase 6 — Deepen (Optional)

If the source connects to existing knowledge in interesting ways:
- Note non-obvious connections between pages
- Flag contradictions with existing pages
- Suggest follow-up questions or knowledge gaps
- Suggest related sources worth exploring

---

## Operation 2: Query

**Triggers:** "What do I know about X?", "Summarize everything about Y", "Compare A and B", or any question that can be answered from wiki content.

### Steps

1. **Read `wiki/index.md`** (L1) — find relevant pages
2. **Read relevant page summaries** (L2) — scan the Overview/Summary sections
3. **Read full pages only if needed** (L3) — for detailed answers
4. **Synthesize answer** with citations — link back to wiki pages using `[[page-name]]`
5. **Prefer wiki content over training data** — the wiki is the source of truth for the user's knowledge
6. **If the wiki doesn't have enough:** say so explicitly. Suggest what to ingest to fill the gap.

### Dual Output Rule

After answering, check: did this query surface new knowledge or connections? If yes, update relevant wiki pages. Every interaction should leave the wiki richer than it was before.

### Archiving (Optional)

Only when the user explicitly asks to save a query result:
- Save as a new `resource` page with `resource_type: archive`
- Link to cited wiki pages in `sources:`
- Prefix the index entry with `[Archived]`
- Append to log

---

## Operation 3: Lint

**Triggers:** "Lint my wiki", "Check wiki health", "Wiki maintenance", "Audit the wiki".

### Deterministic Checks (Auto-Fix)

These can be fixed automatically:

1. **Index consistency**
   - Files in `wiki/pages/` missing from `wiki/index.md` → add with `(no summary)` placeholder
   - Index entries pointing to nonexistent files → mark as `[MISSING]`

2. **Broken internal links**
   - `[[wikilinks]]` pointing to nonexistent pages → search for the target elsewhere. Single match = fix path. Zero or multiple = report to user

3. **Missing frontmatter fields**
   - Pages missing required frontmatter (`domains`, `tags`, `sources`, `related`, `created`, `updated`) → add with placeholder values and set `needs_review: true`

4. **Stale dates**
   - Pages where content was updated but `updated:` date wasn't refreshed → fix date

5. **Frontmatter/body sync**
   - `[[wikilinks]]` in body that aren't in `related:` frontmatter → add to `related:`
   - `related:` entries that don't have corresponding `[[wikilinks]]` in body → add to Related section

### Heuristic Checks (Report Only)

These require human judgment — report findings, don't auto-fix:

1. **Contradictions** — claims in one page that conflict with another
2. **Stale pages** — pages not updated in 30+ days referencing active projects
3. **Orphan pages** — pages with no inbound `[[wikilinks]]` from other pages
4. **Missing pages** — `[[wikilinks]]` that point to pages that don't exist yet
5. **Unreviewed pages** — count of pages with `needs_review: true`
6. **Unsourced claims** — pages with empty `sources:` fields
7. **Domain gaps** — domains with very few pages (might need more knowledge capture)
8. **Missing entity pages** — concepts/people frequently mentioned but lacking their own page
9. **Low-confidence pages** — pages with `confidence: low` that haven't been verified
10. **Tag inconsistency** — similar tags that should be unified (e.g., `saas` vs `SaaS` vs `saas-ideas`)

### Post-Lint

Append to `wiki/log.md`:
```
## [YYYY-MM-DD] lint | {N} issues found, {M} auto-fixed
- Auto-fixed: {list}
- Needs attention: {list}
```

---

## Operation 4: Review

**Triggers:** "Review my wiki", "What needs review?", "Review queue", "Go through unreviewed pages".

A guided walkthrough of pages that need human verification.

### Steps

1. **Scan** all wiki pages for `needs_review: true`
2. **Sort** by priority:
   - `confidence: low` first (most likely to contain errors)
   - `confidence: medium` second
   - Recently created before older pages
3. **Present each page** one at a time:
   - Show the page title, type, domains, confidence level, and source
   - Show the Summary/Overview section (L2 — don't dump the full page)
   - Ask: **"Approve, edit, or skip?"**
4. **On approve:** Set `needs_review: false`, update the `updated:` date
5. **On edit:** The user provides corrections. Apply them, keep `needs_review: true` until they explicitly approve
6. **On skip:** Move to next page, leave `needs_review: true`
7. **After all pages reviewed (or user says stop):** Report summary — how many approved, edited, skipped, remaining

### Post-Review

Append to `wiki/log.md`:
```
## [YYYY-MM-DD] review | {N} pages reviewed
- Approved: {count}
- Edited: {count}
- Skipped: {count}
- Remaining unreviewed: {count}
```

---

## Operation 5: Stats

**Triggers:** "Wiki stats", "How big is my wiki?", "Dashboard", "Wiki overview".

A quick health dashboard. Read `wiki/index.md` and scan frontmatter across pages.

### Output Format

```
Mind Wiki Stats
─────────────────────────
Pages:        {total} total
  Ideas:      {n}  |  Journal:    {n}
  People:     {n}  |  Strategies: {n}
  Technology: {n}  |  Projects:   {n}
  Resources:  {n}

Sources:      {total} raw sources ingested

Domains:      {list of all domains with page counts}
Top tags:     {top 10 most-used tags}

Health:
  Needs review:    {n} pages
  Low confidence:  {n} pages
  No sources:      {n} pages
  Orphan pages:    {n} pages

Last activity: {date and description from log.md}
─────────────────────────
```

Do NOT run a full lint — just surface the key numbers quickly.

---

## Entity Types

Seven types, each with specific frontmatter and required sections. Templates are in `references/`.

| Type | Directory | Key Fields | Required Sections |
|------|-----------|------------|-------------------|
| **Idea** | `pages/ideas/` | status (seed→developing→mature→archived), confidence | Summary, Core Thesis, Implications, Open Questions, Related |
| **Journal** | `pages/journal/` | mood | Entry, Themes, Decisions Made, Open Loops |
| **Person** | `pages/people/` | role | Who, Context, Key Interactions, Notes |
| **Strategy** | `pages/strategies/` | status (draft→active→paused→completed→abandoned), confidence | Goal, Approach, Assumptions, Risks, Steps/Timeline, Progress, Related |
| **Technology** | `pages/technology/` | category, proficiency (learning��familiar→proficient→expert) | What It Is, Why It Matters, Key Concepts, Gotchas, Code Examples, Related |
| **Project** | `pages/projects/` | status (idea→active→paused→completed→abandoned), repo | Overview, Goals, Architecture, Current State, Roadmap, Team/Stakeholders, Related |
| **Resource** | `pages/resources/` | resource_type (article/video/repo/tool/book/course/person), url | Summary, Key Takeaways, Quotes/Highlights, Related |

### Shared Frontmatter (all types)

```yaml
title:          # Page title
type:           # One of the 7 types
domains: []     # Broad categories (personal, tech, business, project names)
tags: []        # Granular free-form tags (saas, mvp, react, productivity)
sources: []     # Where this info came from (file paths or citations)
related: []     # Explicit links to related wiki pages (queryable)
needs_review:   # true for LLM-created content
created:        # YYYY-MM-DD
updated:        # YYYY-MM-DD
```

**`domains` vs `tags`:** Domains are broad, stable categories that define which area of your life/work a page belongs to. Tags are granular, flexible labels for specific topics. A page might have `domains: [tech, business]` and `tags: [saas, ai, convex, mvp]`.

**`related` field:** Explicit list of related page paths. Unlike `[[wikilinks]]` in the body, these are machine-queryable via Obsidian Dataview or grep. Keep both in sync — the Lint operation will flag mismatches.

---

## Cross-Domain Tagging

Every page MUST have a `domains:` field. Use consistent tags:

- `personal` — personal life, reflections, diary
- `tech` — general technology knowledge
- `business` — money strategies, business ideas
- Add project-specific domains as needed (e.g., `zorlgog`, `mind-wiki`)

Shared entities appearing in multiple domains are the most valuable nodes in the graph. Always tag with ALL relevant domains.

---

## File Naming

- **Wiki pages:** kebab-case — `my-cool-idea.md`
- **Journal entries:** date-prefixed — `2026-04-06-reflections.md`
- **Sources:** date-prefixed — `2026-04-06-descriptive-slug.md`
- No special characters except hyphens
- Keep names short but descriptive

---

## Conflict Handling

When new information contradicts existing wiki content:

1. **Never silently overwrite** existing claims
2. Add a `> **Conflict note:**` blockquote annotating the disagreement
3. Cite both sources with dates
4. Set `needs_review: true` on the page
5. Let the human resolve the conflict

---

## Obsidian Integration

This wiki is designed to work seamlessly with Obsidian:

- **`[[wikilinks]]`** render as clickable, navigable links
- **YAML frontmatter** is readable by the Dataview plugin for queries like:
  ```dataview
  TABLE domains, confidence, updated
  FROM "wiki/pages"
  WHERE needs_review = true
  SORT updated DESC
  ```
- **Graph view** visualizes all cross-references — orphan pages are immediately visible as disconnected nodes
- **Tags** in frontmatter are searchable via Obsidian's tag pane
- **Templates** in `references/` can be registered as Obsidian core templates for manual page creation

---

## Remote Access (MCP Server)

The mind-wiki skill works when you're inside the wiki directory. To access your wiki from any other directory or from Claude Desktop, install the optional MCP server:

```
setup mind-wiki mcp
```

This runs `npx mind-wiki-mcp setup` which registers the MCP server in your Claude settings. Once installed, tools like `mind_query`, `mind_stats`, `mind_lint`, `mind_review`, and `mind_pages` are available from any Claude Code project or Claude Desktop conversation.

To uninstall: `npx mind-wiki-mcp uninstall`

---

## Core Principles

1. **Dual output** — every task produces a deliverable AND wiki updates
2. **Classify before extract** — source type determines extraction depth
3. **Index before read** — always check L1 before jumping to L3
4. **Source everything** — every claim traces back to a source
5. **Compound, don't evaporate** — knowledge grows with every interaction
6. **Human verifies** — you write, the human approves
