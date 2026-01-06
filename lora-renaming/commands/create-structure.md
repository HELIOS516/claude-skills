Create the standardized folder structure for character: $ARGUMENTS

1. Create these folders in `1_MODELS_LORA_$ARGUMENTS/`:
   - `LORA_$ARGUMENTS/`
   - `OUTPUTS_$ARGUMENTS/`
   - `_LORA_TRAINING_DATA_$ARGUMENTS/`
   - `_archive/`

2. Inside `LORA_$ARGUMENTS/`, create model-type subfolders based on existing LoRAs:
   - `LORA_SDXL_$ARGUMENTS/` (if SDXL LoRAs exist)
   - `LORA_SD15_$ARGUMENTS/` (if SD15 LoRAs exist)
   - `LORA_FLUX1_$ARGUMENTS/` (if FLUX LoRAs exist)
   - etc.

3. Report what was created

Do NOT move any files yet - just create the folder structure.
