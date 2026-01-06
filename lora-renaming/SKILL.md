---
name: lora-renaming
description: Rename LoRA safetensors files and folders using standardized naming conventions. Use when organizing, renaming, or sorting LoRA model files for Stable Diffusion, SDXL, Flux, or other AI image generation models.
---

# LoRA File Renaming Schema

## Directory Structure

```
1_MODELS_LORA_{CHARACTER}/
├── LORA_{CHARACTER}/
│   ├── _LORA_TRAINING_DATA_{CHARACTER}/
│   │   └── (training datasets, samples, states)
│   ├── LORA_{MODEL}_{CHARACTER}/
│   │   └── LORA_{MODEL}_{CHARACTER}_{VARIANT}_{DATE}_E{MAX}/
│   │       └── (safetensors files)
├── OUTPUTS_{CHARACTER}/
├── _SCRIPTS/
└── _archive/
```

**Model Type Subfolders:**
- `LORA_FLUX1_{CHARACTER}/`
- `LORA_FLUX2_{CHARACTER}/`
- `LORA_SDXL_{CHARACTER}/`
- `LORA_SD15_{CHARACTER}/`
- `LORA_PONY_{CHARACTER}/`
- `LORA_ZIMAGE_{CHARACTER}/`
- `LORA_ZIT_{CHARACTER}/`
- `LORA_CHROMA_{CHARACTER}/`
- `LORA_WAN21_{CHARACTER}/`
- `LORA_WAN22_{CHARACTER}/`
- `LORA_HUNYUAN_{CHARACTER}/`

## Folder Naming Convention

```
LORA_{MODEL}_{CHARACTER}_{VARIANT}_{MMDDYY}_E{MAX}/
```

**Components:**
- `MODEL`: UPPERCASE model type (FLUX1, FLUX2, SDXL, SD15, PONY, ZIMAGE, ZIT, etc.)
- `CHARACTER`: UPPERCASE character name (SIJIA, EVAN, HOLLY, JESS, etc.)
- `VARIANT`: UPPERCASE training variant - **MUST verify from metadata** (AITOOLKIT, KOHYA, H200, LUSTIFY, TURBO, CYBER, ULTRAREALISTIC, BASE, etc.)
- `MMDDYY`: Training date in month-day-year format
- `E{MAX}`: Total epochs trained (e.g., E10, E15, E30)

**Examples:**
- `LORA_FLUX1_SIJIA_AITOOLKIT_110925_E11/`
- `LORA_SDXL_HOLLY_KOHYA_110425_E30/`
- `LORA_SD15_EVAN_BASE_020225_E10/`
- `LORA_ZIMAGE_ASHLEY_TURBO_010126_S4000/` (step-based)

## File Naming Convention

### Epoch-Based Files
```
LORA_{MODEL}_{CHARACTER}_{VARIANT}_{DATE}_E{CURRENT}_E{MAX}.safetensors
```

**Components:**
- `MODEL`: UPPERCASE base model (SDXL, FLUX1, SD15, PONY, etc.)
- `CHARACTER`: UPPERCASE character name
- `VARIANT`: UPPERCASE variant/style name
- `DATE`: MMDDYY format
- `E{CURRENT}`: Current epoch number (zero-padded: E01, E05, E10)
- `E{MAX}`: Maximum/total epochs trained

**Examples:**
- `LORA_SDXL_SIJIA_LUSTIFY_092825_E05_E10.safetensors`
- `LORA_FLUX1_EVAN_BASE_020925_E10_E10.safetensors`
- `LORA_SD15_JESS_CYBER_031125_E15_E15.safetensors`

### Step-Based Files (for step-based training)
```
LORA_{MODEL}_{CHARACTER}_{VARIANT}_{DATE}_S{STEP}.safetensors
```

**Examples:**
- `LORA_ZIMAGE_ASHLEY_TURBO_010126_S2500.safetensors`
- `LORA_ZIT_SIJIA_H200_122825_S1400.safetensors`
- `LORA_CHROMA_SEOLAHH_BASE_120325_S750.safetensors`

## Complete Reorganization Script Template

```python
#!/usr/bin/env python3
"""Reorganize and rename {CHARACTER} LoRA files to standardized format."""

import os
import re
import shutil
from pathlib import Path

def is_date_format(num_str):
    """Check if a string looks like a date (MMDDYY format)."""
    if len(num_str) != 6:
        return False
    try:
        month = int(num_str[:2])
        day = int(num_str[2:4])
        return 1 <= month <= 12 and 1 <= day <= 31
    except ValueError:
        return False

def main():
    base = Path(".")
    CHARACTER = "CHARACTER_NAME"  # Replace with actual character name

    # Create folder structure
    folders = [
        f"LORA_{CHARACTER}/_LORA_TRAINING_DATA_{CHARACTER}",
        f"LORA_{CHARACTER}/LORA_SDXL_{CHARACTER}",
        f"LORA_{CHARACTER}/LORA_SD15_{CHARACTER}",
        f"LORA_{CHARACTER}/LORA_FLUX1_{CHARACTER}",
        f"LORA_{CHARACTER}/LORA_PONY_{CHARACTER}",
        f"OUTPUTS_{CHARACTER}",
        "_SCRIPTS",
    ]
    for f in folders:
        Path(f).mkdir(parents=True, exist_ok=True)
    print("Created folder structure")

    renames = []

    # Find all source folders and their files
    for src_folder in base.iterdir():
        if not src_folder.is_dir() or src_folder.name.startswith(('LORA_', 'OUTPUTS_', '_')):
            continue

        folder_name = src_folder.name

        # Detect model type from folder name
        if folder_name.startswith('SD15_'):
            model = "SD15"
        elif folder_name.startswith('SDXL_'):
            model = "SDXL"
        elif folder_name.startswith('FLUX1_') or folder_name.startswith('FLUX_'):
            model = "FLUX1"
        elif folder_name.startswith('FLUX2_'):
            model = "FLUX2"
        elif folder_name.startswith('PONY_'):
            model = "PONY"
        elif folder_name.startswith('ZIMAGE_'):
            model = "ZIMAGE"
        elif folder_name.startswith('ZIT_'):
            model = "ZIT"
        else:
            continue

        model_dir = f"LORA_{model}_{CHARACTER}"

        # Extract variant, date, and max epochs from folder name
        # Pattern: {MODEL}_{CHARACTER}_{VARIANT}_{DATE}_E{MAX}
        m = re.match(rf'(?:{model}|FLUX)_{CHARACTER}_(.+?)_(\d{{6}})_E(\d+)', folder_name)
        if m:
            variant = m.group(1).replace('_', '')
            date = m.group(2)
            max_epochs = m.group(3)
        else:
            # Simpler pattern without variant
            m = re.match(rf'(?:{model}|FLUX)_{CHARACTER}_(\d{{6}})_E(\d+)', folder_name)
            if m:
                variant = "BASE"
                date = m.group(1)
                max_epochs = m.group(2)
            else:
                continue

        dest_folder = f"LORA_{model}_{CHARACTER}_{variant}_{date}_E{max_epochs}"
        dest_path = base / f"LORA_{CHARACTER}/{model_dir}/{dest_folder}"
        dest_path.mkdir(parents=True, exist_ok=True)

        for f in src_folder.glob("*.safetensors"):
            # Handle epoch format: {CHAR}_{MODEL}_{VARIANT}_LORA_{DATE}_EPOCH_{NN}_{MAX}
            m = re.search(r'EPOCH_(\d+)_(\d+)', f.name)
            if m:
                current = m.group(1).zfill(2)
                max_ep = m.group(2)
                date_m = re.search(r'(\d{6})_EPOCH', f.name)
                file_date = date_m.group(1) if date_m else date
                new_name = f"LORA_{model}_{CHARACTER}_{variant}_{file_date}_E{current}_E{max_ep}.safetensors"
                renames.append((f, dest_path / new_name))
                continue

            # Handle step format
            m = re.search(r'_0+(\d+)\.safetensors$', f.name)
            if m:
                step = m.group(1)
                new_name = f"LORA_{model}_{CHARACTER}_{variant}_{date}_S{step}.safetensors"
                renames.append((f, dest_path / new_name))

    # SAFETY CHECK: Detect destination collisions
    destinations = {}
    collisions = []
    for src, dst in renames:
        dst_str = str(dst)
        if dst_str in destinations:
            collisions.append((src, dst, destinations[dst_str]))
        else:
            destinations[dst_str] = src

    if collisions:
        print(f"\n=== COLLISION DETECTED - ABORTING ===")
        print(f"Found {len(collisions)} files that would overwrite each other:")
        for src, dst, existing in collisions[:5]:
            print(f"  {src.name} -> {dst.name}")
            print(f"    Would overwrite: {existing.name}")
        print("\nFix the regex patterns before running again!")
        return  # ABORT - don't proceed with moves

    # Preview
    print(f"\n=== Preview ===")
    print(f"Files to rename: {len(renames)}")
    for src, dst in renames[:5]:
        print(f"  {src.name} -> {dst.name}")
    if len(renames) > 5:
        print(f"  ... ({len(renames) - 5} more)")

    # Execute
    print("\n=== Executing ===")
    success = 0
    for src, dst in renames:
        # Double-check destination doesn't exist
        if dst.exists():
            print(f"  SKIPPED (exists): {dst.name}")
            continue
        try:
            shutil.move(str(src), str(dst))
            success += 1
        except Exception as e:
            print(f"  FAILED: {src.name} -> {e}")
    print(f"Renamed: {success}/{len(renames)}")

    # Move training data folders
    for td in [f"_{CHARACTER.lower()}_training_data", "_training_data"]:
        if Path(td).exists():
            try:
                shutil.move(td, f"LORA_{CHARACTER}/_LORA_TRAINING_DATA_{CHARACTER}/{td}")
                print(f"Moved {td}")
            except:
                pass

    # Cleanup empty source folders
    for folder in base.iterdir():
        if folder.is_dir() and not folder.name.startswith(('LORA_', 'OUTPUTS_', '_')):
            try:
                if not any(folder.iterdir()):
                    folder.rmdir()
                    print(f"Removed empty: {folder.name}")
            except:
                pass

if __name__ == "__main__":
    main()
```

## Extracting Metadata from Safetensors

Key metadata fields to extract:
- `software`: Training software info (ai-toolkit stores here)
- `ss_sd_scripts_commit_hash`: Present if trained with Kohya
- `ss_base_model_version`: Base model (flux1, sdxl_base_v1-0, sd_v1, etc.)
- `modelspec.date`: Training date (ISO format, convert to MMDDYY)
- `ss_epoch`: Current file's epoch number (Kohya)
- `training_info.step` / `training_info.epoch`: Step/epoch (ai-toolkit)
- `ss_num_epochs`: Total epochs trained
- `ss_output_name`: Output name used during training
- `sshs_model_hash`: For deduplication
- `sshs_legacy_hash`: Legacy hash for deduplication

## Metadata Extraction Script

```python
import json

def get_lora_metadata(filepath):
    with open(filepath, 'rb') as f:
        header_size = int.from_bytes(f.read(8), 'little')
        header = json.loads(f.read(header_size))
        metadata = header.get('__metadata__', {})
    return metadata

def detect_training_software(metadata):
    """Detect which software was used to train the LoRA."""
    # Check for ai-toolkit
    if 'software' in metadata:
        software = metadata['software']
        if isinstance(software, str):
            software = json.loads(software)
        if software.get('name') == 'ai-toolkit':
            return 'AITOOLKIT'

    # Check for Kohya
    if 'ss_sd_scripts_commit_hash' in metadata:
        return 'KOHYA'

    return 'BASE'  # Unknown/default
```

## Base Model Mapping

| Metadata Value | Short Code |
|----------------|------------|
| flux1 | FLUX1 |
| flux2 | FLUX2 |
| sdxl_base_v1-0 | SDXL |
| sd_v1 | SD15 |
| sd_v2 | SD20 |
| pony | PONY |
| z-image turbo | ZIMAGE |
| zit | ZIT |
| hunyuan | HUNYUAN |
| wan2.1 | WAN21 |
| wan2.2 | WAN22 |
| chroma | CHROMA |

## Training Software Detection (CRITICAL)

**ALWAYS check metadata before naming the VARIANT.** Different training tools store software info differently:

| Metadata Field | Training Software | VARIANT Name |
|----------------|-------------------|--------------|
| `software.name` = "ai-toolkit" | ostris/ai-toolkit | AITOOLKIT |
| `ss_sd_scripts_commit_hash` present | kohya-ss/sd-scripts | KOHYA |
| `modelspec.implementation` = "diffusers" | Diffusers-based | Check `software` field |

**ai-toolkit metadata example:**
```json
"software": {"name": "ai-toolkit", "repo": "https://github.com/ostris/ai-toolkit", "version": "0.7.8"}
"training_info": {"step": 2750, "epoch": 25}
```

**Kohya metadata example:**
```
"ss_sd_scripts_commit_hash": "abc123..."
"ss_training_started_at": "1234567890"
```

## Epoch Extraction Patterns

In order of priority (check most specific patterns first):

1. **EPOCH_N_M format**: `EPOCH_01_11` → E01_E11
2. **Kohya step format**: `_11_15E_S2409` → E11_E15
3. **Simple epoch**: `_e01.safetensors` → E01
4. **Epoch in name**: `epoch09` → E09
5. **SDXL numbered**: `-000001.safetensors` → E01
6. **Step-based**: `_S1750.safetensors` → S1750
7. **Date+epoch**: `_092825_01.safetensors` (date + epoch) → E01
8. **Two numbers at end**: `_10_15.safetensors` → E10_E15

## Safety Rules (CRITICAL)

**NEVER use bash for file renaming** - bash regex (`BASH_REMATCH`) can silently fail, causing all files to get the same destination name and overwrite each other.

**Always use Python scripts** with these safety checks:
1. **Collision detection** - Before executing, check if multiple source files would map to the same destination
2. **Abort on collision** - If collisions detected, print error and exit without moving any files
3. **Skip existing** - During execution, skip if destination file already exists
4. **Validate regex matches** - Only add to renames list if the regex actually matched

The template script above includes these safety checks.

## Batch Renaming Workflow

1. **Analyze folder structure** - List all existing LoRA folders and safetensors files
2. **Create target structure** - Create LORA_{CHARACTER}/ hierarchy with model-type subfolders
3. **Create Python rename script** - Save as `rename_{character}.py` in the character folder
4. **Run in preview mode** - Check proposed renames before executing
5. **Execute renames** - Move and rename files
6. **Move training data** - Move training data to `_LORA_TRAINING_DATA_{CHARACTER}/`
7. **Move outputs** - Move outputs to `OUTPUTS_{CHARACTER}/`
8. **Clean up** - Remove empty source folders
9. **Remove duplicates** - Check for `_COPY` suffix or identical hashes

## Deduplication

When reorganizing, check for duplicates:
- Files with `_COPY` or ` copy` suffix are likely duplicates - verify with MD5 hash
- Files with identical `sshs_model_hash` metadata are duplicates
- Keep the file with the cleaner name, archive/delete duplicates

```bash
# Compare hashes to detect duplicates
md5 -q "file1.safetensors"
md5 -q "file2.safetensors"

# Remove duplicate _COPY files
find . -name "*_COPY.safetensors" -delete
```

## Handoff Document Template

When working across sessions, create a `CLAUDE_CODE_HANDOFF.md` with:
- Project overview and target schema
- Character inventory with file counts
- Completed work
- Pending tasks
- Known issues
- File paths reference

## Characters Directory

All character LoRAs are stored in:
```
/Users/fee4jk/Library/CloudStorage/GoogleDrive-evan@evandecan.com/My Drive/1_STABLE_DIFFUSION/1_MODELS/1_MODELS_LORA/1_MODELS_LORA_CHARACTERS/
```

Subdirectories: `1_MODELS_LORA_{CHARACTER}/` for each character.


## Slash Commands

This skill includes reusable slash commands for Claude Code. Copy the `commands/` folder to your project's `.claude/commands/` directory.

### Available Commands

| Command | Usage | Description |
|---------|-------|-------------|
| `inventory` | `/project:inventory ASHLEY` | Inventory a character's LoRA files |
| `create-structure` | `/project:create-structure MISTY` | Create standardized folder structure |
| `gen-rename-script` | `/project:gen-rename-script JESS` | Generate Python rename script |
| `organize-character` | `/project:organize-character SARAH` | Full end-to-end workflow |

### Installation

```bash
# Copy commands to your project
cp -r ~/claude-skills/lora-renaming/commands/ /path/to/your/project/.claude/commands/

# Copy CLAUDE.md template
cp ~/claude-skills/lora-renaming/CLAUDE.md.template /path/to/your/project/CLAUDE.md
```

### Workflow (Boris Pattern)

1. **CLAUDE.md** - Project memory, updated when Claude makes mistakes
2. **Slash commands** - Repeatable workflows with `$ARGUMENTS` for character names
3. **Plan Mode** - `Shift+Tab` twice before non-trivial tasks
4. **Skills** - This skill provides the naming schema knowledge

When Claude does something wrong, add it to CLAUDE.md:
```markdown
## DO NOT
- Use bash for file renaming (BASH_REMATCH fails silently)
```

This creates compounding improvements over time.


## Subagents

This skill includes a verification subagent. Copy `agents/` to your project's `.claude/agents/` directory.

### Available Agents

| Agent | Description |
|-------|-------------|
| `verify-renames` | Validates rename operations completed successfully |

### Installation

```bash
# Copy agents to your project
cp -r ~/claude-skills/lora-renaming/agents/ /path/to/your/project/.claude/agents/
```


## Verification (CRITICAL)

> "Give Claude a way to verify its work. If Claude has that feedback loop, it will 2-3x the quality of the final result." - Boris Cherny

### Pre-Execution Verification

Before running any rename script:

1. **Count source files**: `find . -name "*.safetensors" | wc -l`
2. **Check collision detection**: Script must abort if multiple files → same destination
3. **Preview mode**: Review proposed renames before executing
4. **Check for regex failures**: Ensure patterns actually matched

### Post-Execution Verification

After running rename script:

```bash
# Verify file count unchanged
find . -name "*.safetensors" | wc -l

# Check naming compliance
find . -name "*.safetensors" -exec basename {} \; | head -20

# Find potential duplicates
find . -name "*.safetensors" -exec basename {} \; | sort | uniq -d

# Find empty folders (cleanup candidates)
find . -type d -empty

# Verify folder structure
ls -la LORA_*/
```

### Verification Checklist

- [ ] Same file count before/after
- [ ] All files follow naming convention
- [ ] No duplicate filenames
- [ ] No orphaned files in root
- [ ] Empty source folders cleaned up
- [ ] Training data moved correctly
- [ ] Outputs moved correctly


## Parallel Execution (Boris Pattern)

For organizing multiple characters simultaneously:

### Git Worktree Strategy
```bash
# Create isolated worktrees for parallel work
git worktree add ../lora-ashley -b feature/ashley
git worktree add ../lora-misty -b feature/misty
```

### iTerm2 Setup
1. Open 5 terminal tabs (numbered 1-5)
2. Preferences → Profiles → Terminal → ✓ "Send notification on idle"
3. Run one character per tab:
   - Tab 1: `/project:organize-character ASHLEY`
   - Tab 2: `/project:organize-character COURTNEY`
   - Tab 3: `/project:organize-character EVAN`

Each session is independent, preventing conflicts.


## Troubleshooting

### Common Issues

| Issue | Cause | Fix |
|-------|-------|-----|
| All files renamed to same name | Bash regex failure | Use Python script |
| Files overwritten | No collision detection | Add abort-on-collision |
| Wrong model type | Metadata not read | Extract from safetensors |
| Missing epoch info | Regex didn't match | Add more patterns |
| Date format wrong | Not MMDDYY | Parse ISO and convert |

### Debug Commands

```python
# Check metadata for a specific file
from pathlib import Path
import json

def get_metadata(path):
    with open(path, 'rb') as f:
        size = int.from_bytes(f.read(8), 'little')
        return json.loads(f.read(size)).get('__metadata__', {})

meta = get_metadata("file.safetensors")
print(json.dumps(meta, indent=2))
```

### Recovery

If renames went wrong:
1. Check git status (if files tracked)
2. Restore from backup/archive
3. Use file history (Google Drive has versioning)
4. Cross-reference with metadata hashes
