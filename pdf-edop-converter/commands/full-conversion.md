Full end-to-end chapter conversion: $ARGUMENTS

Arguments format: CHAPTER_NUMBER START_PAGE END_PAGE
Example: `/project:full-conversion 42 158 159`

## Complete Workflow

This command runs the full conversion pipeline with approval gates.

### Phase 1: Extract (auto)
- Extract chapter from PDF
- Save raw text and formatting JSON
- Report extraction summary

**PAUSE for review of extraction quality**

### Phase 2: Analyze Logic (auto)
- Identify indication variants
- Map branch points
- Document conditionals
- Create decision flowchart

**PAUSE for review of logic structure**

### Phase 3: Generate EDOP Name
- Propose name following schema: EDOP + ANATOMY + APPROACH + PROCEDURE
- Explain reasoning

**PAUSE for approval of name**

### Phase 4: Convert to Template
- Apply UVA template structure
- Convert italic/bold text per rules
- Build logical branching with proper markers:
  - `=== BRANCH ===` for major choices
  - `[IF]...[/IF]` for conditionals
  - `{a | b | c}` for inline selects
  - `[OPTIONAL]...[/OPTIONAL]` for optional content
- Create Findings section (or enhance if exists)
- Add all SmartLinks

**PAUSE for template review**

### Phase 5: Verify
- Run verification checklist
- Check all sections present
- Validate logical flow
- Confirm SmartLinks correct

### Phase 6: Finalize
- Save final template as `EDOP{NAME}.txt`
- Update `CONVERSION_STATUS.md`
- Create `chapter_{N}_conversion_summary.md`

## Output Files

1. `chapter_{N}_raw.txt` - Raw extraction
2. `chapter_{N}_formatting.json` - Formatting metadata
3. `chapter_{N}_logic_map.md` - Decision tree analysis
4. `chapter_{N}_conversion_summary.md` - Conversion documentation
5. `EDOP{NAME}.txt` - Final template

## Important

- Use Plan Mode (Shift+Tab twice) at start
- Pause after each phase for human review
- Document all decisions in conversion summary
- Test that each logical path reads correctly
