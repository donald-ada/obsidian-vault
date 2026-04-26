---
name: health-check
description: This skill should be used when the user asks to "health check the knowledge base", "audit my Obsidian wiki", "check the knowledge base for broken links", "find orphan articles in my Obsidian vault", "lint the wiki", or wants to detect structural issues, stale content, missing index entries, or wiki-archival candidates in the daily notes of the Obsidian knowledge base.
---

# Wiki Health Check Workflow

Audit the Obsidian knowledge base for structural issues, drift between daily notes and curated wiki, and content worth promoting.

**Output language:** report findings in the user's working language. The English headings in this skill are documentation only — render the actual report in the user's language. Concrete identifiers (file paths, function names, error codes) are preserved verbatim.

## Determine Vault Path

Read `VAULT_PATH` from `~/.claude/obsidian-vault.conf`. If missing, stop and tell the user: "Vault path is not configured. Please run `/anthrosidian:setup`."

## Checks to Perform

### Structural integrity (Critical)

1. **Broken wikilinks** — find `[[links]]` pointing to non-existent articles
   - Read all wiki articles + daily notes; extract all `[[wikilinks]]`; verify each target exists
2. **Index completeness** — every article in `wiki/<topic>/` appears in `wiki/<topic>/_index.md`; every topic appears in `wiki/_index.md`
3. **Topic _index is in three-column format** — every `wiki/<topic>/_index.md` table has columns `文章 | 摘要 | 关键词` (per `references/wiki-rules.md`). Two-column format is **legacy** and must be flagged for upgrade.

### Content quality (Warnings)

4. **Orphan articles** — articles not linked from any index or other article
5. **Stale articles** — `raw/` source modified after the wiki article's `updated:` frontmatter date
6. **Missing `aliases:` frontmatter** — every article in `wiki/<topic>/<name>.md` should have an `aliases:` field with 3-8 alternates. Articles missing it lose Obsidian's alias-resolution lookup.
7. **Thin Keywords column** — topic-index rows whose `关键词` column has fewer than 5 keywords. Surfaces routing weaknesses.

### Daily → Wiki drift (Suggestions — the "mining" step)

8. **Recurring problems not yet promoted** — scan all `daily/**/*.md` (recursive, year-sharded) `## Problems & Solutions` headings:
   - Cluster `### <title>` headings across days by token overlap (or by exact match if titles are identical)
   - Flag clusters with **≥ 3 occurrences** as wiki-article candidates
   - For each candidate, list: dates, original titles, and any wikilinks already attached. Suggest a topic placement.
9. **Frequently linked wikilinks** — count `[[wiki/...]]` references across all daily notes:
   - Articles linked from **≥ 5 days** are confirmed high-traffic — verify their Keywords column is rich
   - Wikilinks pointing to **non-existent** articles in daily notes (a special case of check #1, but daily-side)
10. **Untouched topics** — topics whose articles haven't been linked from any daily note in the last 60 days. Either dead knowledge or undiscoverable; flag for review.
11. **Daily notes never sourced into wiki** — daily notes whose P&S entries are not linked from any wiki article and have no `→ [[wiki/...]]` outbound link. If their problem clusters meet check #8, propose elevation; otherwise leave alone.

### Data inconsistencies (Warnings)

12. **Conflicting facts** — same identifier (function name, error code, version) described differently across articles on the same topic

## Output Format

Group findings by severity. Within each severity, group by check number so the user can act on related items together.

```markdown
# Wiki Health Check · <YYYY-MM-DD>

## Critical (must fix)
### Broken wikilinks (N)
- `daily/<YYYY>/<YYYY-MM-DD>.md` → `[[wiki/<topic>/<missing-article>]]` (target missing)
- ...

### Topic _index in legacy two-column format (N)
- `wiki/<topic>/_index.md` — upgrade to three-column (文章 | 摘要 | 关键词)
- ...

## Warnings (should fix)
### Articles missing `aliases:` frontmatter (N)
- `wiki/<topic>/<article>.md`
- ...

### Stale articles (N)
- `wiki/<topic>/<article>.md` (updated <YYYY-MM-DD>) ← `raw/<source>.md` modified <YYYY-MM-DD>
- ...

## Suggestions (mining — promote daily → wiki)
### Recurring problems worth a wiki article (N clusters)
- **"<problem cluster title>"** — N occurrences (<dates>). Existing wikilink: `[[wiki/<topic>/<existing-article>]]` partially covers it. Suggest a focused article: `wiki/<topic>/<proposed-slug>.md`.
- ...

### High-traffic wiki articles (≥ 5 daily-note references)
- `wiki/<topic>/<article>` — N references; Keywords column has M entries → expand
- ...

### Untouched topics (60-day cooldown)
- `<topic>` — last daily reference <YYYY-MM-DD>
```

Keep the report scannable. **No fluff.** Empty sections are omitted — if no broken links, drop that subsection entirely.

## Fix Mode

If the user asks "fix these" / "fix automatically", apply only the **mechanical** fixes without further confirmation:
- Add missing entries to `wiki/<topic>/_index.md` (using existing article frontmatter to infer summary)
- Sync `wiki/_index.md` article counts and `updated:` field
- Bump `updated:` field on touched indexes

For everything else (upgrading legacy _index format, adding `aliases:`, promoting daily problems to wiki articles), surface the candidate plus a one-line action and **ask before applying**. These changes are subjective enough that batch automation will produce noise.

## Mining Heuristics

- **Cluster matching** for check #8: titles that share ≥ 3 content words after dropping stopwords. Punctuation differences and minor wording variations should still cluster.
- **Topic placement suggestion**: pick the topic whose articles have the highest keyword-overlap with the cluster's title and bodies. If no topic clearly matches, suggest creating a new topic and explain why.
- **Skip ephemeral problems**: clusters whose entries all resolve in their own daily note (no follow-up next day) and have no concrete identifiers worth grepping later. These are noise, not patterns.
