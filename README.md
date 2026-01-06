# Claude Skills

Personal collection of Claude Code skills for extending Claude's capabilities with specialized knowledge and workflows.

## Installation

### Option 1: Symlink entire repo (recommended)
```bash
# Clone the repo
git clone https://github.com/HELIOS516/claude-skills.git ~/claude-skills

# Symlink to Claude's skills directory
ln -sf ~/claude-skills ~/.claude/skills
```

### Option 2: Copy individual skills
```bash
cp -r ~/claude-skills/lora-renaming ~/.claude/skills/
```

## Skills

| Skill | Description |
|-------|-------------|
| [lora-renaming](./lora-renaming/) | Standardized naming conventions for LoRA safetensors files and folders |

## Using Skills with Projects

Each skill can include:
- `SKILL.md` - Main skill definition (auto-loaded by Claude Code)
- `commands/` - Slash commands for repeatable workflows
- `CLAUDE.md.template` - Project memory template

### Setting Up a Project

```bash
cd /path/to/your/project

# Copy slash commands
mkdir -p .claude/commands
cp ~/claude-skills/lora-renaming/commands/*.md .claude/commands/

# Copy CLAUDE.md template and customize
cp ~/claude-skills/lora-renaming/CLAUDE.md.template ./CLAUDE.md
```

### Using Slash Commands

In Claude Code:
```
/project:inventory ASHLEY
/project:create-structure MISTY
/project:organize-character SARAH
```

## Adding New Skills

1. Create a new folder: `mkdir new-skill-name`
2. Add `SKILL.md` with frontmatter:
   ```yaml
   ---
   name: skill-name
   description: Brief description
   ---
   ```
3. Optionally add `commands/` folder with slash commands
4. Optionally add `CLAUDE.md.template` for project setup
5. Commit and push

## Syncing Across Machines

After cloning on a new machine:
```bash
git clone https://github.com/HELIOS516/claude-skills.git ~/claude-skills
ln -sf ~/claude-skills ~/.claude/skills
```

To update:
```bash
cd ~/claude-skills && git pull
```

## License

Personal use.
