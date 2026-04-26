# Wiki Article Rules

## Article Format

```markdown
---
tags: [topic-tag]
aliases: [synonym-1, abbreviation, "error message snippet", "older name"]
source: "[[raw/source-file]]"
created: YYYY-MM-DD
updated: YYYY-MM-DD
---

# Article Title

Content with [[wikilinks]] to related concepts.

## See Also
- [[related-article-1]]
- [[related-article-2]]
```

**Rules for the `aliases` frontmatter field:**
- 3-8 alternate names the user might use to reach this article
- Include English ↔ Chinese pairs when relevant (e.g., `["内部链接", "internal link"]`)
- Include common abbreviations (e.g., `["EtM", "encrypt-then-mac"]`)
- Include error messages or specific symptoms users might quote
- Wikilinks `[[alias]]` resolve to the canonical article when listed here, so picking good aliases is the second line of defense after the topic index Keywords column

## Conventions

- **Language**: Chinese for all wiki content unless source is English-only
- **File naming**: Chinese titles are fine; use hyphens for multi-word English names
- **Granularity**: One concept per article — prefer many small articles over few large ones
- **Source linking**: Always link back to raw source via `source` frontmatter
- **Callouts**: Use Obsidian callouts (`> [!note]`, `> [!warning]`) for emphasis
- **References**: When moving content between directories, update all references

## Index Format

### Topic index (`wiki/<topic>/_index.md`)

Use a three-column table. The **Keywords** column is critical for agent routing — include all concepts, parameter names, error terms, and synonyms an agent might search for.

```markdown
---
tags: [index]
updated: YYYY-MM-DD
---

# <Topic Name>

| 文章 | 摘要 | 关键词 |
|------|------|--------|
| [[wiki/topic/article-name\|Article Title]] | 2-3 sentence summary covering the problem, solution, and key gotchas | keyword1, keyword2, param-name, error-term, synonym |
| [[wiki/topic/another-article\|Another Title]] | ... | ... |
```

**Rules for the Keywords column:**
- Include parameter names, function names, error messages an agent might quote
- Include synonyms and alternate phrasings (e.g., "内部链接, internal link, hyperlink, 跳转")
- Include the specific tool/library names in both English and Chinese if relevant
- Aim for 8-15 keywords per article

### Master index (`wiki/_index.md`)

```markdown
---
tags: [index]
updated: YYYY-MM-DD
---

# Wiki Master Index

| Topic | Articles | Description |
|-------|----------|-------------|
| [[wiki/topic/_index\|Topic Name]] | N | One-line description covering key sub-topics |
```

**Rules:**
- Link to `_index.md` of each topic, NOT directly to articles
- Description should name specific sub-topics (not just the domain name)
- Sync article count and `updated:` date on every change
