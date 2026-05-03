---
name: setup
description: This skill should be used when the user runs /anthrosidian:setup in Claude Code, runs anthrosidian:setup in Codex, wants to configure the anthrosidian plugin, or asks to set up their vault path. Use to initialize or update plugin configuration.
argument-hint: [vault-path]
allowed-tools: [Bash]
---

# Obsidian Vault Plugin Setup

Configure the anthrosidian plugin by recording the vault path.

## Configuration File

The plugin reads vault settings from:
- Codex: `~/.codex/obsidian-vault.conf`
- Claude Code: `~/.claude/obsidian-vault.conf`

Format:
```
VAULT_PATH="/absolute/path/to/your/vault"
```

## Setup Process

1. **Determine vault path**
   - If `$ARGUMENTS` is non-empty, use it directly as the vault path
   - Otherwise ask the user: "Please enter the full path to your Obsidian vault (e.g. /Users/yourname/Documents/My Vault):"

2. **Validate** the path exists:
   ```bash
   test -d "<vault-path>" && echo OK || echo NOT_FOUND
   ```
   - If NOT_FOUND, warn the user and ask to confirm or enter a different path

3. **Write config**:
   - In Codex, write `~/.codex/obsidian-vault.conf`
   - In Claude Code, write `~/.claude/obsidian-vault.conf`
   - If the active agent cannot be determined, ask one focused clarifying question before writing

   Codex:
   ```bash
   mkdir -p ~/.codex
   printf 'VAULT_PATH="%s"\n' "<vault-path>" > ~/.codex/obsidian-vault.conf
   ```

   Claude Code:
   ```bash
   mkdir -p ~/.claude
   printf 'VAULT_PATH="%s"\n' "<vault-path>" > ~/.claude/obsidian-vault.conf
   ```

4. **Confirm success** with a summary:
   - Config saved to the active agent config path
   - Today's daily note path: `<vault-path>/daily/<YYYY>/YYYY-MM-DD.md` (sharded by year)
   - Active skills: daily-log, compile-wiki, qa-wiki, health-check
   - To reconfigure: `anthrosidian:setup [new-path]` in Codex or `/anthrosidian:setup [new-path]` in Claude Code
