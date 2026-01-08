Extract chapter from PDF: $ARGUMENTS

Arguments format: CHAPTER_NUMBER START_PAGE END_PAGE
Example: `/project:extract-chapter 42 158 159`

## Process

1. **Locate PDF:** Find "Operative Dictations in General and Vascular Surgery - Unknown.pdf"

2. **Extract text with formatting:**
   - Use PyMuPDF (fitz) to extract text with font metadata
   - Capture italic, bold, bold-italic flags
   - Preserve page numbers for reference

3. **Save outputs:**
   - `chapter_{N}_raw.txt` - Plain text extraction
   - `chapter_{N}_formatting.json` - Text with formatting metadata

4. **Analyze patterns:**
   - Count italic segments (variables/options)
   - Count bold segments (headers)
   - Count bold-italic segments (conditionals)
   - Report summary

## Output Format

```
=== Chapter {N} Extraction Summary ===
Pages: {START} - {END}
Raw text lines: {count}
Italic segments: {count}
Bold segments: {count}
Bold-italic segments: {count}

Files created:
- chapter_{N}_raw.txt
- chapter_{N}_formatting.json

Ready for conversion with /project:convert-chapter {N}
```

## Important

- Do NOT convert yet - just extract
- Verify page numbers are correct before extracting
- Check that "Template Operative Dictation" appears in extracted text
