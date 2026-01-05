# Claude Skills

Personal collection of Claude Code skills for extending Claude's capabilities with specialized knowledge and workflows.

## Installation

### Option 1: Symlink entire repo
```bash
# Clone the repo
git clone https://github.com/YOUR_USERNAME/claude-skills.git ~/claude-skills

# Symlink to Claude's skills directory
ln -s ~/claude-skills ~/.claude/skills
```

### Option 2: Copy individual skills
```bash
cp -r ~/claude-skills/lora-renaming ~/.claude/skills/
```

## Skills

| Skill | Description |
|-------|-------------|
| [lora-renaming](./lora-renaming/) | Standardized naming conventions for LoRA safetensors files and folders used in Stable Diffusion, SDXL, Flux, and other AI image generation models |

## Skill Structure

Each skill follows the Claude Code skill format:
```
skill-name/
└── SKILL.md    # Main skill definition with instructions
```

## Usage

Once installed, Claude Code will automatically detect and use these skills when relevant tasks are requested.

## Adding New Skills

1. Create a new folder with your skill name
2. Add a `SKILL.md` file with frontmatter:
   ```yaml
   ---
   name: skill-name
   description: Brief description of what the skill does
   ---
   ```
3. Add your skill content below the frontmatter
4. Commit and push

## License

Personal use.
