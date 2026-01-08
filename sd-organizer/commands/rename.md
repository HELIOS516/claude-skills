# Rename Command

Rename files to standardized format with metadata extraction.

## Usage
```
/sd-organize:rename {CHARACTER} [--type lora|image|all]
```

## Workflow

### 1. Inventory
Run inventory first to understand scope.

### 2. Preview
Show all proposed renames:
```
FROM: sijia_lora_h200_optimized_000000800.safetensors
  TO: LORA_FLUX2_SIJIA_H200_OPTIMIZED_122925_S800.safetensors
  Source: metadata (step=800)
```

### 3. Collision Detection
ABORT if any collisions detected:
```
=== COLLISION DETECTED - ABORTING ===
file1.safetensors -> SAME_NAME.safetensors
file2.safetensors -> SAME_NAME.safetensors
```

### 4. Execute
Only after user approval.

### 5. Verify
Confirm file counts match.

## For LoRAs
- Extract step/epoch from safetensors metadata FIRST
- Fall back to filename patterns only if metadata missing
- Format: `LORA_{MODEL}_{CHAR}_{VARIANT}_{DATE}_E{N}_E{MAX}.safetensors`

## For Images
- Extract prompt/seed from PNG chunks or JPG EXIF FIRST
- Fall back to A1111 filename parsing
- Format: `{char}_{date}_{tags}_{model}_{source}_{seed}_{counter}.{ext}`
