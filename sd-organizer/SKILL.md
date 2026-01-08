---
name: sd-organizer
description: Unified skill for organizing Stable Diffusion files - both image outputs (PNG/JPG) and LoRA models (safetensors). Use when organizing, sorting, or renaming SD output images or LoRA files.
---

# SD Organizer Skill

Unified skill for organizing Stable Diffusion files: both **image outputs** (PNG/JPG) and **LoRA models** (safetensors).

## Core Principles (Apply to ALL File Types)

### 1. Metadata is SOURCE OF TRUTH
**Always extract embedded metadata FIRST.** Filenames are often inconsistent, but metadata is authoritative.

```python
# CORRECT workflow:
info = extract_metadata(filepath)  # PRIMARY
if info.step is None and info.epoch is None:
    info = extract_from_filename(filename)  # FALLBACK only
```

### 2. Python Only (NO BASH for File Operations)
Bash regex (`BASH_REMATCH`) fails silently and causes files to overwrite each other. Always use Python scripts.

### 3. Collision Detection (CRITICAL)
```python
# Before ANY move operation:
destinations = {}
for src, dst in renames:
    if str(dst) in destinations:
        ABORT("Collision detected!")
    destinations[str(dst)] = src
```

### 4. Preview → Execute Workflow
1. **Preview** all proposed changes
2. **Review** with user
3. **Execute** only after approval
4. **Verify** file counts match

---

## Directory Schema

```
1_MODELS_LORA_{CHARACTER}/
├── LORA_{CHARACTER}/                        # LoRA safetensors
│   ├── LORA_{MODEL}_{CHARACTER}/            # Model-type grouping
│   │   └── LORA_{MODEL}_{CHAR}_{VARIANT}_{DATE}_E{MAX}/
│   │       └── *.safetensors
│   └── _LORA_TRAINING_DATA_{CHARACTER}/     # Training datasets
├── OUTPUTS_{CHARACTER}/                     # Image outputs
│   ├── OUTPUTS_{MODEL}_{CHARACTER}/         # SD outputs by model
│   ├── GEMINI_{CHARACTER}/                  # AI sources
│   ├── VERTEX_{CHARACTER}/
│   ├── GROK_{CHARACTER}/
│   ├── VIDEO_{CHARACTER}/
│   ├── _UNSORTED_{CHARACTER}/
│   └── _archive/duplicates/
├── _SCRIPTS/
└── _archive/
```

**Model Types:** FLUX1, FLUX2, SDXL, SD15, PONY, ZIMAGE, CHROMA, WAN21, WAN22, HUNYUAN

---

## Part 1: Image Organization

### Image Naming Convention
```
{character}_{date}_{description}_{model}_{source}_{seed}_{counter}.{ext}
```

Example: `sijia_120325_blonde_bikini_beach_standing_sdxl_forge_1234567890_00001.png`

### Image Metadata Extraction

**Priority order:**
1. PNG `parameters` chunk (A1111/Forge)
2. PNG `prompt` + `workflow` (ComfyUI)
3. JPG EXIF UserComment JSON (Wavespeed) - **use exiftool**
4. JPG EXIF tag 37510 (A1111)
5. A1111 filename: `{counter}-{seed}-{prompt}.ext`
6. File modification date (fallback)

### Wavespeed EXIF Extraction (CRITICAL - use exiftool)

Wavespeed embeds metadata as JSON in EXIF UserComment field. **PIL may fail** with "Invalid EXIF text encoding" - use exiftool instead.

```python
import subprocess
import json

def extract_wavespeed_metadata(filepath):
    """Extract metadata from Wavespeed images using exiftool.

    Wavespeed stores JSON in EXIF UserComment:
    {"id": "...", "model": "wavespeed-ai/z-image/turbo-lora",
     "prompt": "...", "seed": 12345, "created_at": "..."}

    PIL fails with 'Invalid EXIF text encoding for UserComment'.
    Exiftool handles this correctly.
    """
    result = subprocess.run(
        ['exiftool', '-json', '-UserComment', '-ImageDescription', str(filepath)],
        capture_output=True, text=True
    )
    if result.returncode == 0:
        data = json.loads(result.stdout)[0]
        user_comment = data.get('UserComment', '')
        if user_comment and user_comment.startswith('{'):
            return json.loads(user_comment)
    return {}

# Usage
meta = extract_wavespeed_metadata('image.jpeg')
prompt = meta.get('prompt', '')
seed = meta.get('seed')
model = meta.get('model')  # e.g. "wavespeed-ai/z-image/turbo-lora"
```

**Model mapping for Wavespeed:**
| API Model | Short Code |
|-----------|------------|
| `wavespeed-ai/z-image/turbo-lora` | ZIMAGE |
| `wavespeed-ai/flux-2-dev/text-to-image-lora` | FLUX2 |
| `bytedance/seedream-v4.5/edit-sequential` | SEEDREAM |

### JPG EXIF UTF-16 Decoding (A1111)

A1111 stores params in EXIF tag 37510 with UNICODE header + UTF-16:

```python
def extract_jpg_exif(filepath):
    with Image.open(filepath) as img:
        exif = img._getexif()
        if exif and 37510 in exif:
            user_comment = exif[37510]
            if isinstance(user_comment, bytes):
                if user_comment.startswith(b'UNICODE'):
                    data = user_comment[8:]  # Skip 8-byte header
                    for encoding in ['utf-16', 'utf-16-be', 'utf-16-le']:
                        try:
                            decoded = data.decode(encoding, errors='ignore')
                            if any(c in decoded for c in 'aeiou'):  # Validate
                                return decoded
                        except:
                            continue
                    # Fallback: remove null bytes
                    return data.replace(b'\x00', b'').decode('utf-8', errors='ignore')
    return ""
```

### A1111 Filename Parsing (Fallback)
```python
# Format: {counter}-{seed}-{prompt}.ext
# Example: 00011-3341201837-POV photography.jpg
match = re.match(r'^(\d{5,})-(\d{8,})-(.+)\.(png|jpg|jpeg)$', filename)
if match:
    counter, seed, prompt = match.groups()[:3]
```

---

## Part 2: LoRA Organization

### LoRA Naming Convention

**Folders:**
```
LORA_{MODEL}_{CHARACTER}_{VARIANT}_{MMDDYY}_E{MAX}/
LORA_{MODEL}_{CHARACTER}_{VARIANT}_{MMDDYY}_S{MAX}/  # step-based
```

**Files:**
```
LORA_{MODEL}_{CHARACTER}_{VARIANT}_{DATE}_E{CURRENT}_E{MAX}.safetensors
LORA_{MODEL}_{CHARACTER}_{VARIANT}_{DATE}_S{STEP}.safetensors
```

Examples:
- `LORA_SDXL_SIJIA_LUSTIFY_092825_E05_E10.safetensors`
- `LORA_FLUX2_SIJIA_H200_OPTIMIZED_122925_S1000.safetensors`

### LoRA Metadata Extraction (PRIMARY)

```python
import json

def get_lora_metadata(filepath):
    """Extract metadata from safetensors. ALWAYS call this first."""
    with open(filepath, 'rb') as f:
        header_size = int.from_bytes(f.read(8), 'little')
        header = json.loads(f.read(header_size))
        return header.get('__metadata__', {})

def extract_training_info(filepath):
    metadata = get_lora_metadata(filepath)
    result = {'step': None, 'epoch': None, 'max_epoch': None, 'software': None}

    # ai-toolkit: training_info as JSON string
    if 'training_info' in metadata:
        info = metadata['training_info']
        if isinstance(info, str):
            info = json.loads(info)
        result['step'] = info.get('step')
        result['epoch'] = info.get('epoch')

    # ai-toolkit: software info
    if 'software' in metadata:
        sw = metadata['software']
        if isinstance(sw, str):
            sw = json.loads(sw)
        if sw.get('name') == 'ai-toolkit':
            result['software'] = 'AITOOLKIT'

    # Kohya: direct fields
    if 'ss_epoch' in metadata:
        result['epoch'] = int(metadata['ss_epoch'])
    if 'ss_num_epochs' in metadata:
        result['max_epoch'] = int(metadata['ss_num_epochs'])
    if 'ss_sd_scripts_commit_hash' in metadata:
        result['software'] = 'KOHYA'

    return result
```

### LoRA Filename Patterns (FALLBACK ONLY)

Use these patterns ONLY if metadata extraction returns None:

1. `EPOCH_01_11` → E01_E11
2. `_11_15E_S2409` → E11_E15 (Kohya)
3. `[-_]0+(\d+).safetensors` → step number
4. `_S1750.safetensors` → S1750
5. `_e01.safetensors` → E01
6. `_01_10.safetensors` → E01_E10

---

## Workflow (6-Phase Boris Pattern)

### Phase 1: Inventory
```bash
# Count files by type
find . -name "*.safetensors" | wc -l
find . -name "*.png" -o -name "*.jpg" | wc -l
```

### Phase 2: Preview
- Show all proposed renames
- Display source → destination mapping
- Highlight any issues (no metadata, collisions)

### Phase 3: Resolve Issues
- Handle files with missing metadata
- Get user input for ambiguous cases

### Phase 4: Execute
- Create destination folders
- Move/rename with collision detection
- Track success/failure

### Phase 5: Verify
```python
assert count_before == count_after, "File count mismatch!"
```

### Phase 6: Cleanup
- Remove empty source folders
- Update logs

---

## Safety Rules

### DO
- Extract metadata FIRST
- Use Python for all file operations
- Check collisions before ANY move
- Preview all changes before executing
- Verify file counts after operations

### DO NOT
- Use bash for file renaming
- Overwrite files without collision check
- Process `_training_states/` or cache files
- Rename `.json`, `.yaml`, `.txt` files
- Skip metadata extraction

---

## sd-toolkit Integration

Location: `/Users/fee4jk/Documents/GitHub/sd-image-gen/eagle-lora-tools/sd-toolkit/`

```bash
# Analyze single image
sd-toolkit analyze /path/to/image.png

# Preview batch operation
sd-toolkit preview /path/to/folder

# Execute with organization
sd-toolkit process /path/to/folder --organize
```

Key components:
- `extractor.py` - Metadata extraction (PNG, JPG EXIF, filename)
- `tagger.py` - 1,831 tag patterns
- `renamer.py` - Schema-based naming
- `organizer.py` - File organization

---

## Characters Directory

```
/Users/fee4jk/Library/CloudStorage/GoogleDrive-evan@evandecan.com/My Drive/
  1_STABLE_DIFFUSION/1_MODELS/1_MODELS_LORA/1_MODELS_LORA_CHARACTERS/
```

| Character | Output Files | LoRA Files | Status |
|-----------|--------------|------------|--------|
| SIJIA | 2,073 | 303 | ✅ Complete |
| MISTY | 10,870 | 131 | Outputs ✅, LoRAs pending |
| JESS | 5,136 | 92 | Outputs ✅, LoRAs pending |
| MICHELLE | 4,714 | 59 | Outputs ✅, LoRAs pending |
| SARAH | 3,524 | 248 | Outputs ✅, LoRAs pending |
| ASHLEY | 1,400 | 38 | Outputs ✅, LoRAs pending |
| EVAN | 767 | 20 | Outputs ✅, LoRAs pending |
| SEOLAHH | 825 | 66 | Outputs ✅, LoRAs pending |
| SARAH_HSU | 150 | 121 | Outputs ✅, LoRAs pending |

---

## Slash Commands

| Command | Description |
|---------|-------------|
| `/sd-organize:inventory` | Analyze folder contents |
| `/sd-organize:rename` | Full rename workflow |
| `/sd-organize:verify` | Verify organization |

---

## Verification Checklist

After any operation:
- [ ] File count before == file count after
- [ ] All files follow naming convention
- [ ] No duplicate filenames
- [ ] No orphaned files in root
- [ ] Empty source folders cleaned
- [ ] Metadata extracted (not filename-only)

---

## Rsync Workflow for Large Folders

Google Drive is extremely slow for file operations. Use rsync instead:

```bash
# 1. Sync folder to local staging
rsync -avP "gdrive_path/folder/" /tmp/sd-organize-staging/

# 2. Process locally with sd-toolkit (fast)
cd /tmp/sd-organize-staging
sd-toolkit organize . -o local-only -m metadata --lora-char-base /path/to/1_MODELS_LORA_CHARACTERS

# 3. Sync organized files back to Google Drive
rsync -avP /tmp/sd-organize-staging/OUTPUTS_* "gdrive_path/"
```

**Benefits:**
- Processing locally takes seconds vs minutes over Google Drive
- Avoid network timeouts and slow API calls
- Can run multiple organize operations in parallel

---

## Recent Fixes (January 2026)

### Fix 1: ComfyUI Comprehensive Text Scan
**File:** `src/sd_toolkit/core/models.py`

The extractor now scans ALL nodes for text > 30 chars, not just specific node types. Uses a **scoring system** to find the best image prompt:
- Penalizes system prompts ("you are", "ai assistant", "instructions")
- Rewards image indicators (woman, asian, bedroom, bikini, etc.)
- Rewards comma-separated format and weight syntax like `(asian:2)`

### Fix 2: ttN textCycleLine Full Extraction (Jan 8, 2026)
**Problem:** `ttN textCycleLine` nodes store multi-line prompts. Extractor was taking only one line based on index, returning just `"Seolahh,"` instead of full prompt.

**Solution:** Join all lines:
```python
full_text = " ".join(line.strip() for line in text.strip().split("\n") if line.strip())
```

### Fix 3: Skip Specialized CLIPTextEncode Nodes (Jan 8, 2026)
**Problem:** FaceDetailer workflows have multiple CLIPTextEncode nodes ("Eye Prompt", "Lips Prompt", etc.) that were being picked as main prompt.

**Solution:** Skip nodes with these title keywords:
```python
SPECIALIZED_PROMPT_KEYWORDS = [
    "eye", "lips", "hair", "skin", "nipple", "face", "body",
    "hands", "feet", "background", "style", "lighting", "detail",
    "pussy", "vulva", "breast", "chest", "torso", "arm", "leg",
]
```

### Fix 4: Improved Clothing Patterns (Jan 8, 2026)
**Problem:** `" dress "` didn't match "wearing black dress" (end of string).

**Solution:** Patterns now match end-of-string:
```python
"dress": [" dress", "dress,", "black dress", "red dress", ...]
```
Added: skirt, stockings, heels, corset, bodysuit

---

## Troubleshooting

| Issue | Cause | Fix |
|-------|-------|-----|
| All files same name | Bash regex failure | Use Python script |
| Files overwritten | No collision check | Add abort-on-collision |
| Wrong epoch | Metadata not read | Extract from safetensors |
| JPG no metadata | UTF-16 not decoded | Use multi-encoding fallback |
| Date as epoch | Regex matched date | Check is_date_format() first |
| No prompt extracted | Custom ComfyUI nodes | Uses comprehensive text scan fallback |
| Slow processing | Google Drive API | Use rclone workflow |
| Only first line extracted | ttN textCycleLine node | Fixed: now joins all lines |
| Eye/Lips prompt used | FaceDetailer workflow | Fixed: skips specialized nodes |
| "dress" not detected | Pattern needs trailing space | Fixed: matches end-of-string |
