---
name: compile-wiki
description: This skill should be used when the user asks to "compile the wiki", "process raw notes into the knowledge base", "turn raw sources into wiki articles in my Obsidian vault", "ingest this URL into the knowledge base", or wants to transform unstructured material in raw/ into structured wiki articles in the Obsidian knowledge base.
---

# Wiki Compilation Workflow

Compile raw source materials in `raw/` into structured wiki articles in `wiki/`.

## Step 1: Ingest (if URL provided)

Use `obsidian:defuddle` to fetch the URL, save as `raw/<title>.md`.

## Step 2: Read and Extract

Read the raw source thoroughly. Extract:
- Key concepts and definitions
- Facts, data points, and examples
- Relationships between ideas
- Practical patterns and gotchas

## Step 3: Create or Update Wiki Articles

Use `obsidian:obsidian-markdown` for correct formatting.
For article format and naming conventions, see: `references/wiki-rules.md`

- One concept per article (prefer many small articles over few large ones)
- Save to `wiki/<topic>/<article-name>.md`
- Use `[[wikilinks]]` for cross-references between articles
- Use `![[embeds]]` for images in `assets/`
- Add YAML frontmatter: `tags`, `aliases`, `source`, `created`, `updated`
  - `aliases:` lists alternate names the article can be reached by — synonyms, abbreviations, error messages, or older naming the user might remember. Obsidian resolves `[[alias]]` wikilinks to the canonical article when listed here. Aim for 3-8 aliases.

## Step 4: Update Topic Index

Each topic directory must have `wiki/<topic>/_index.md`. Create it if it doesn't exist.

Use a **three-column table** (see `references/wiki-rules.md` for exact format):

| 文章 | 摘要 | 关键词 |
|------|------|--------|
| [[wiki/topic/article\|Title]] | 2-3 sentence summary of the problem, solution, and key gotchas | keyword1, param-name, error-term, synonym |

**The Keywords column must be comprehensive** — include parameter names, function/class names, error messages, synonyms, and alternate phrasings. This is the primary way agents navigate to the right article without opening every file.

## Step 5: Update Master Index

`wiki/_index.md` — links to each topic's `_index.md` (NOT to individual articles).

**Required on every update:**
- Link target must be `[[wiki/<topic>/_index\|<Topic>]]`, never a direct article link
- Description should name specific sub-topics covered, not just the domain name
- Sync the `updated:` field in the YAML frontmatter to today's date
- Keep article count in sync
