# Claude Skills

Personal collection of Claude Code skills based on Boris Cherny's workflow patterns.

> See [BORIS_PATTERNS.md](./BORIS_PATTERNS.md) for the complete reference guide.

## Quick Start

```bash
# Clone and symlink
git clone https://github.com/HELIOS516/claude-skills.git ~/claude-skills
ln -sf ~/claude-skills ~/.claude/skills
```

## Repository Structure

```
claude-skills/
├── BORIS_PATTERNS.md          # Master reference for all patterns
├── common/                     # Shared components for any project
│   ├── commands/
│   │   └── commit-push-pr.md  # Daily driver command
│   ├── agents/
│   │   ├── code-simplifier.md # Post-task simplification
│   │   └── verify-work.md     # Task verification
│   ├── hooks/
│   │   └── hooks.json         # PostToolUse, Stop hooks
│   └── templates/
│       ├── settings.json      # Pre-allowed permissions
│       ├── mcp.json           # MCP server configs
│       └── claude-github-action.yml
└── lora-renaming/              # Domain-specific skill
    ├── SKILL.md
    ├── CLAUDE.md.template
    ├── commands/
    └── agents/
```

## Skills

| Skill | Description |
|-------|-------------|
| [common](./common/) | Shared commands, agents, hooks, and templates |
| [lora-renaming](./lora-renaming/) | LoRA safetensors file organization |

## Setting Up a New Project

### 1. Copy CLAUDE.md Template
```bash
cd /path/to/project
cp ~/claude-skills/lora-renaming/CLAUDE.md.template ./CLAUDE.md
# Customize for your project
```

### 2. Copy Slash Commands
```bash
mkdir -p .claude/commands
cp ~/claude-skills/common/commands/*.md .claude/commands/
cp ~/claude-skills/lora-renaming/commands/*.md .claude/commands/
```

### 3. Copy Agents
```bash
mkdir -p .claude/agents
cp ~/claude-skills/common/agents/*.md .claude/agents/
cp ~/claude-skills/lora-renaming/agents/*.md .claude/agents/
```

### 4. Copy Hooks (Optional)
```bash
mkdir -p .claude/hooks
cp ~/claude-skills/common/hooks/hooks.json .claude/hooks/
```

### 5. Copy Settings (Optional)
```bash
cp ~/claude-skills/common/templates/settings.json .claude/settings.json
```

## Key Patterns (Boris Workflow)

### CLAUDE.md (Shared Memory)
- Single file at project root, checked into git
- Update when Claude makes mistakes: error → rule
- Whole team contributes
- Auto-injected into every session

### Plan Mode First
- `Shift+Tab` twice to enter Plan mode
- Iterate until plan is solid
- Switch to auto-accept for execution

### Slash Commands
- `.claude/commands/*.md`
- Pre-compute with inline bash: `$(git status)`
- `/commit-push-pr` for daily workflow

### Subagents
- `.claude/agents/*.md`
- `code-simplifier` - Post-task cleanup
- `verify-work` - Validation checks

### Verification Loops
- Always give Claude a way to verify work
- Domain-specific: tests, CLI, browser
- 2-3x quality improvement

## Example Usage

```bash
# Daily workflow
/commit-push-pr Fix epoch parsing

# LoRA project
/project:inventory ASHLEY
/project:organize-character MISTY

# After task completion
Run the code-simplifier agent
Run verify-work on my changes
```

## Syncing Across Machines

```bash
# First time setup
git clone https://github.com/HELIOS516/claude-skills.git ~/claude-skills
ln -sf ~/claude-skills ~/.claude/skills

# Update
cd ~/claude-skills && git pull
```

## Adding New Skills

1. Create folder: `mkdir skill-name`
2. Add `SKILL.md` with frontmatter
3. Add `commands/`, `agents/` as needed
4. Add `CLAUDE.md.template` for project setup
5. Commit and push

## Resources

- [Boris's Original Thread](https://x.com/bcherny/status/2007179832300581177)
- [Claude Code Docs](https://code.claude.com/docs)
- [Slash Commands](https://code.claude.com/docs/en/slash-commands)
- [Subagents](https://code.claude.com/docs/en/sub-agents)
- [Hooks Guide](https://code.claude.com/docs/en/hooks-guide)
