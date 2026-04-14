---
name: daily-log
description: This skill should be used when the user asks to "log this to my Obsidian knowledge base", "record this in my daily note", "save this problem and solution to my knowledge base", "write this to my Obsidian daily log", or wants to capture a technical note, bug fix, insight, or solved problem into their Obsidian vault's daily note.
---

# Daily Development Log Workflow

Write structured problem/solution entries into today's Obsidian daily note.

## Determine Vault Path

Read `VAULT_PATH` from `~/.claude/obsidian-vault.conf`.
If the file is missing or `VAULT_PATH` is not set, stop and tell the user:
"Vault path is not configured. Please run `/obsidian-vault:setup` to set it up."

## Daily Note Path

`<VAULT>/daily/<YYYY-MM-DD>.md` (today's date)

## If Daily Note Doesn't Exist

Create it with this template (use `obsidian:obsidian-cli` or write directly):

```markdown
---
tags: [daily]
date: YYYY-MM-DD
---

# YYYY-MM-DD

## Tasks
- [ ] 

## Problems & Solutions

## Technical Notes
```

## Entry Format

Append under `## Problems & Solutions`:

```markdown
### <Problem Title>
**Context:** which project / scenario
**Problem:** what went wrong or what was encountered
**Solution:** how it was resolved
**Why it works:** brief explanation (1-2 sentences)
```

Append reusable insights under `## Technical Notes`.

## After Writing

If the insight is reusable beyond today's specific code (patterns, API quirks, tool behaviors), offer to file it into `wiki/<topic>/` as a standalone article.
Link from daily note: `→ [[wiki/<topic>/<article-name>]]`

## When Adding a Wiki Article

After creating `wiki/<topic>/<article-name>.md`, **always** update `wiki/_index.md`:

1. **Link format**: Use Obsidian wikilink, not Markdown link:
   ```
   | [[wiki/topic/article-name\|topic]] | N | Description |
   ```
2. **Sync frontmatter**: Update `updated:` in the YAML frontmatter to today's date.
3. **Sync article count**: Increment the Articles column and the Stats section.

These three steps are mandatory — skip any one and the index becomes stale.
