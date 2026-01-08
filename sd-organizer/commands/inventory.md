# Inventory Command

Analyze a folder's contents for organization.

## Usage
```
/sd-organize:inventory {PATH or CHARACTER}
```

## Behavior

1. **Detect file types:**
   - Count `.safetensors` files (LoRAs)
   - Count `.png`, `.jpg`, `.jpeg` files (images)

2. **For LoRAs:**
   - List all safetensors by folder
   - Extract metadata sample (step, epoch, software)
   - Identify naming patterns

3. **For Images:**
   - Count by extension
   - Sample metadata extraction
   - Identify sources (ComfyUI, A1111, AI-generated)

4. **Report:**
   - Total files by type
   - Files with/without metadata
   - Suggested organization

## Example Output

```
=== INVENTORY: ASHLEY ===
LoRAs: 38 safetensors across 6 folders
Images: 1,400 files (PNG: 1,200, JPG: 200)

LoRA Folders:
  ASHLEY_SDXL_V1_092825_E10/ - 10 files
  ASHLEY_ZIT_H200_123125/ - 15 files
  ...

Image Sources:
  With A1111 metadata: 1,100
  With ComfyUI metadata: 200
  No metadata: 100

Recommendation: Run /sd-organize:rename ASHLEY
```
