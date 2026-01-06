Organize character LoRAs end-to-end: $ARGUMENTS

Full workflow for `1_MODELS_LORA_$ARGUMENTS/`:

## Phase 1: Inventory
- List all folders and safetensors
- Identify model types and naming patterns
- Report current state

## Phase 2: Plan (wait for approval)
- Propose folder structure
- Show sample renames
- Identify potential issues

## Phase 3: Create Structure
- Create LORA_$ARGUMENTS/, OUTPUTS_$ARGUMENTS/, etc.
- Create model-type subfolders

## Phase 4: Generate Rename Script
- Create Python script with collision detection
- Run preview mode
- Wait for approval before executing

## Phase 5: Execute
- Run rename script
- Move training data folders
- Move output images
- Clean up empty folders

## Phase 6: Verify
- Confirm new structure
- Update CLAUDE.md status

IMPORTANT: Pause after each phase for review. Use Plan Mode (Shift+Tab twice).
