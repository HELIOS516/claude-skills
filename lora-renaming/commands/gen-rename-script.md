Generate a Python rename script for character: $ARGUMENTS

Using the lora-renaming skill as reference:

1. Analyze existing safetensors files in `1_MODELS_LORA_$ARGUMENTS/`
2. Extract metadata from a sample file to determine:
   - Training software (ai-toolkit vs kohya)
   - Base model version
   - Epoch/step format
3. Create `rename_$ARGUMENTS.py` script that:
   - Has collision detection (abort if multiple files → same destination)
   - Skips files in `_training_states/` folders
   - Skips `.json`, `.yaml`, `.txt` files
   - Previews all renames before executing
   - Uses proper epoch padding (E01, E05, E10)
4. Save script to `1_MODELS_LORA_$ARGUMENTS/_SCRIPTS/`

Reference the lora-renaming skill for naming conventions and safety checks.
