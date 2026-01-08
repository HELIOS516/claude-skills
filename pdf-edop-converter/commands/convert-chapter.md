Convert extracted chapter to EDOP template: $ARGUMENTS

Argument: CHAPTER_NUMBER (must have been extracted first)
Example: `/project:convert-chapter 42`

## Prerequisites

- `chapter_{N}_raw.txt` must exist
- `chapter_{N}_formatting.json` must exist

## Conversion Process

### Phase 1: Analysis
1. Read raw text and formatting JSON
2. Identify "Template Operative Dictation" section start
3. Skip intro material (Indication bullets, Essential Steps, etc.)
4. Map source sections to EDOP structure
5. **Identify decision tree:**
   - What are the major BRANCH points (mutually exclusive techniques)?
   - What are the IF-CONDITIONAL sections (indication-specific content)?
   - What are the SELECT options (inline choices)?
   - What are the OPTIONAL sections?

### Phase 2: EDOP Name Generation
Generate name using pattern: `EDOP` + `[ANATOMY/DISEASE]` + `[APPROACH]` + `[PROCEDURE]`

### Phase 3: Build Logical Structure

**Use this marker system:**

1. **Major Branches (pick one):**
```
=== SECTION NAME (select one) ===

--- Option A: Name ---
[complete technique A]
--- End Option A ---

--- Option B: Name ---
[complete technique B]
--- End Option B ---

=== END SECTION NAME ===
```

2. **Conditionals (include if true):**
```
[IF CONDITION]
Content that only applies when condition is met.
Multiple lines allowed.
[/IF]
```

3. **Inline choices:**
```
{option1 | option2 | option3}
```
First option = most common/default

4. **Fill-ins with type hints:**
```
[___:measurement cm]
[___:count]
[describe:symptoms]
```

5. **Optional content:**
```
[OPTIONAL]
Content that may or may not be included.
[/OPTIONAL]
```

### Phase 4: Template Creation

1. Create UVA template header with all SmartLinks
2. Build Indications with patient demographics
3. Create Findings section:
   - Core findings (all cases)
   - Indication-specific findings in [IF] blocks
4. Build Description of Procedure:
   - Group related content together
   - Major technique branches clearly marked
   - Indication-specific steps in [IF] blocks
5. Add standard footer sections

### Phase 5: Quality Check

Verify:
- [ ] Logical flow matches surgical decision-making
- [ ] All branches have complete standalone content
- [ ] Conditionals properly opened and closed
- [ ] SmartLinks correct
- [ ] Professional tone throughout

## Output Files

1. `EDOP{NAME}.txt` - Final template with proper logic structure
2. `chapter_{N}_conversion_summary.md` - Documentation of decisions made

## Important

- Use Plan Mode for complex procedures
- Document all conversion decisions
- Keep indication-specific content grouped together
- Test that each branch/path reads correctly standalone
