---
name: pdf-edop-converter
description: Convert PDF operative dictation chapters into UVA Epic EDOP templates. Use when extracting surgical procedures from medical textbooks and formatting them as electronic operative notes.
---

# PDF to EDOP Converter

## Overview

Converts chapters from "Operative Dictations in General and Vascular Surgery" (and similar surgical textbooks) into UVA Epic Electronic Documentation of Operative Procedures (EDOP) templates.

## Source PDF Information

**Primary Source:** "Operative Dictations in General and Vascular Surgery"
- Total Pages: 838
- Total Chapters: 277
- Coverage: Esophageal, Gastric, Small Bowel, Colorectal, Hepatobiliary, Hernia, Breast, Endocrine, Access/Transplant, Vascular

## EDOP Naming Schema

Pattern: `EDOP` + `[ANATOMY/DISEASE]` + `[APPROACH]` + `[PROCEDURE]`

### Components:
- **Anatomy/Disease:** UPPERCASE anatomic region or pathology (e.g., INGUINALHERNIA, SMALLBOWEL, VENTRALHERNIA)
- **Approach:** Surgical approach if relevant (LAP, OPEN, ROBOTIC)
- **Procedure:** Key procedure characteristic (MESH, RESECTION, LYSIS, PLICATION)

### Examples:
| Chapter | EDOP Name | Components |
|---------|-----------|------------|
| Ch 23: Lap Plication of Perforated Ulcer | EDOPPERFORATEDPEPTICULCERLAPPLICATION | PERFORATEDPEPTICULCER + LAP + PLICATION |
| Ch 42: Lap Enterolysis for SBO | EDOPSMALLBOWLOBSTRUCTIONLAPLYSIS | SMALLBOWLOBSTRUCTION + LAP + LYSIS |
| Ch 43: Small Bowel Resection | EDOPSMALLBOWELRESECTION | SMALLBOWEL + RESECTION |
| Ch 125: Mesh Repair Inguinal Hernia | EDOPINGUINALHERNIAOPENMESH | INGUINALHERNIA + OPEN + MESH |
| Ch 131: Lap Ventral Hernia Repair | EDOPVENTRALHERNIALAP | VENTRALHERNIA + LAP |

## UVA Template Structure

All EDOP templates follow this exact structure:

```
OPERATIVE NOTE

Date/Time:
@TODAYDATE@ @NOW@

@ORCASETRKSTARTSTOP@

Patient:
@NAME@

MRN:
@MRN@

Surgical Team:
Primary and Assisting Surgeons:
@ORSURGPANEL@

Additional Surgical Staff:
@ORSSTAFF@

Procedure:
@ORPROCALL@

Preoperative Diagnosis:
@ORDXCPRE@

Postoperative Diagnosis:
@ORPOSTDX@

Anesthesia:
@ORANEST@

Indications:
@NAME@ is a @AGE@ @SEX@ who [clinical presentation and indication].

Findings:
[Numbered list OR procedure-specific SmartList like {EDOPCHOLEFINDINGS:55621}]

Description of Procedure:
[Detailed procedural narrative with logical branching]

Fluids / I&O:
  Crystalloid/Colloid: ***
  Blood: ***
  EBL: @ORBLDLOSS@
  UOP: ***

Specimens:
@ORSPEC@

Drains:
***

Implants:
@ORIMPLANTWACT@

Wound Class:
{EDLISTWOUNDCLASS:54468}

Complications:
NONE ***

Disposition:
{EDLISTDISPO:54467}

@SIG@
```

## SmartLink Reference

### Patient Demographics
- `@NAME@` - Patient name
- `@MRN@` - Medical record number
- `@AGE@` - Patient age
- `@SEX@` - Patient sex
- `@LEFTRIGHT@` - Laterality (left/right)

### Date/Time
- `@TODAYDATE@` - Today's date (standard format)
- `@IPTODAYDATE@` - Inpatient today's date
- `@LASTSURGDATE@` - Last surgery date
- `@ORDATE@` - OR date
- `@NOW@` - Current time
- `@ORCASETRKSTARTSTOP@` - Case tracking start/stop times

### Procedure
- `@ORPROCALL@` - All procedures
- `@ORPROCLAT@` - Procedure with laterality

### Surgical Team
- `@ORSURGPANEL@` - Primary and assisting surgeons
- `@ORSSTAFF@` - Additional surgical staff

### Diagnoses
- `@ORDXCPRE@` - Preoperative diagnosis
- `@ORPOSTDX@` - Postoperative diagnosis
- `@ORDXCODE@` - Diagnosis code

### Anesthesia
- `@ORANEST@` - Anesthesia type
- `@ORAMBANEST@` - Ambulatory anesthesia
- `@ORANESSTAFF@` - Anesthesia staff

### Fluids & I/O
- `@ORCRYSTAL@` - Crystalloid
- `@ORCOLLOID@` - Colloid
- `@ORTRANSFUSED@` - Blood products
- `@ORBLDLOSS@` - Estimated blood loss
- `@ORURINEOUTPUT@` - Urine output
- `@RRIOCURSHIFT@` - I/O current shift (summary format)

### Labs (inline)
- `@RESUFAST(WBC,RBC,HGB,etc)@` - Lab results fast format
- `@BRIEFLAB(ast:1,alt:1)@` - Brief lab format

### Operative Data
- `@ORSPEC@` - Specimens (basic)
- `@ORSPECPG@` - Specimens (page format)
- `@ORSPECMN@` - Specimens (menu format)
- `@ORDRAIN@` - Drains
- `@ORIMPLANT@` - Implants (basic)
- `@ORIMPLANTWACT@` - Implants with activity

### SmartLists (Procedure-Specific)
- `{EDLISTWOUNDCLASS:54468}` - Wound classification
- `{EDLISTDISPO:54467}` - Disposition
- `{EDOPCHOLEFINDINGS:55621}` - Cholecystectomy findings
- `{EDOPFASCIAL CLOSURE:55622}` - Fascial closure options
- `{YES/NO:11200}` - Yes/No
- `{:37740}` - Patient condition
- `{:88071}` - Intraoperative occurrences
- `{:88072}` - Wound type
- `{:75376}` - Emergency case (within 12 hours)
- `{:88073}` - Highest level resident surgeon
- `{:88074}` - Level of residency supervision
- `{zz_blank reuse:89749::"opt1","opt2"}` - Inline options
- `{WITH SCAL:44890}` - With symptoms

### Signature
- `@SIG@` - Electronic signature

## Fill Point Formatting

### Multiple Choice Options
```
*** [option1 / option2 / option3] ***
```
Example: `*** [direct / indirect / femoral / pantaloon] *** inguinal hernia`

### Measurement Fill Points
```
*** ___ unit ***
```
Example: `Hernia defect measuring *** ___ x ___ cm ***`

### Conditional Steps
```
*** IF CONDITION: action ***
```
Example: `*** IF MALE: The testis was gently pulled down into its anatomic position in the scrotum. ***`

### Alternative Techniques
```
*** OPTION: Choice A / Choice B ***
```
Example: `*** OPTION: Veress needle entry / Hasson technique ***`

### Narrative Fill Points
```
*** [describe: ___] ***
```
Example: `*** [describe symptoms: pain, bulge, discomfort, etc.] ***`

## PDF Extraction Rules

### Chapter Structure Recognition

**SKIP these sections (intro material):**
- Chapter number/title page
- "Indication" bullet lists
- "Essential Steps" numbered lists
- "Note These Variations" sections
- "Complications" lists (pre-procedure, not findings)
- Author bylines and citations

**EXTRACT starting from:**
- "Template Operative Dictation" header
- Or first occurrence of "Preoperative Diagnosis"

### Text Formatting Conversion

#### Italic Text (Variables/Options)
| Source Pattern | Template Conversion |
|---------------|---------------------|
| `___-year-old male/female` | `@AGE@-year-old @SEX@` |
| `left/right` | `@LEFTRIGHT@` |
| Single blank `___` | `*** ___ ***` |
| `option1/option2/option3` | `*** [option1 / option2 / option3] ***` |

#### Bold-Italic Text (Conditionals)
| Source Pattern | Template Conversion |
|---------------|---------------------|
| `If male:` | `*** IF MALE: [action] ***` |
| `If indirect hernia:` | `*** IF INDIRECT HERNIA: [action] ***` |
| `[Choose One:]` | Branching options or simplified conditional |

#### Bold Text (Section Headers)
- Keep as section headers if they map to EDOP structure
- Convert to fill points if they represent choices

### Creating Missing Sections

**If source lacks "Findings" section:**
Create based on structures examined in Description:
```
Findings:
1. [Primary pathology with options: type, size, location]
2. [Associated findings: adhesions, inflammation, etc.]
3. [Tissue quality: integrity, vascularity]
4. [Other pathology: none / describe]
5. [Any unexpected findings]
```

## Python Extraction Script Template

```python
#!/usr/bin/env python3
"""Extract and analyze a chapter from surgical PDF."""

import fitz  # PyMuPDF
import json
import re
from pathlib import Path

def extract_chapter(pdf_path: str, start_page: int, end_page: int, chapter_num: int):
    """Extract chapter text with formatting metadata."""
    doc = fitz.open(pdf_path)

    raw_text = []
    formatting = []

    for page_num in range(start_page - 1, end_page):  # 0-indexed
        page = doc[page_num]
        blocks = page.get_text("dict")["blocks"]

        for block in blocks:
            if "lines" not in block:
                continue
            for line in block["lines"]:
                for span in line["spans"]:
                    text = span["text"].strip()
                    if not text:
                        continue

                    font = span["font"]
                    flags = span["flags"]

                    # Detect formatting
                    is_bold = "Bold" in font or (flags & 2**4)
                    is_italic = "Italic" in font or (flags & 2**1)

                    raw_text.append(text)
                    formatting.append({
                        "text": text,
                        "bold": is_bold,
                        "italic": is_italic,
                        "font": font,
                        "page": page_num + 1
                    })

    doc.close()

    # Save outputs
    base = Path(f"chapter_{chapter_num}")

    with open(f"{base}_raw.txt", "w") as f:
        f.write("\n".join(raw_text))

    with open(f"{base}_formatting.json", "w") as f:
        json.dump(formatting, f, indent=2)

    return raw_text, formatting

def analyze_formatting(formatting: list) -> dict:
    """Analyze italic/bold patterns for conversion."""
    patterns = {
        "italic_segments": [],
        "bold_segments": [],
        "bold_italic_segments": []
    }

    for item in formatting:
        if item["bold"] and item["italic"]:
            patterns["bold_italic_segments"].append(item["text"])
        elif item["bold"]:
            patterns["bold_segments"].append(item["text"])
        elif item["italic"]:
            patterns["italic_segments"].append(item["text"])

    return patterns

if __name__ == "__main__":
    import sys
    if len(sys.argv) < 4:
        print("Usage: python extract_chapter.py <pdf_path> <start_page> <end_page> <chapter_num>")
        sys.exit(1)

    pdf_path = sys.argv[1]
    start_page = int(sys.argv[2])
    end_page = int(sys.argv[3])
    chapter_num = int(sys.argv[4])

    extract_chapter(pdf_path, start_page, end_page, chapter_num)
```

## Conversion Workflow

### Phase 1: Extract
1. Identify chapter page range in PDF
2. Run extraction script to get raw text and formatting
3. Review formatting JSON for italic/bold patterns

### Phase 2: Analyze
1. Identify template dictation section (skip intro material)
2. Map source sections to EDOP structure
3. Create Findings section if missing
4. Identify branching points and conditionals

### Phase 3: Convert
1. Apply SmartLink substitutions
2. Convert italic variables to fill points
3. Convert bold-italic conditionals
4. Format options with *** markers
5. Add standard end-of-case elements

### Phase 4: Verify
1. Check all UVA sections present
2. Verify SmartLinks are correct
3. Validate fill point formatting
4. Ensure chronological flow
5. Run verification agent

## Quality Checklist

- [ ] All UVA template sections present
- [ ] All required SmartLinks included
- [ ] Signature block uses @SIG@
- [ ] EDOP naming follows schema
- [ ] Chronological operative flow maintained
- [ ] Optional steps clearly marked with ***
- [ ] Conditional steps use IF format
- [ ] Equipment/technique options identified
- [ ] Gender-specific steps flagged
- [ ] Standard end-of-case elements included
- [ ] Professional tone maintained
- [ ] Consistent formatting throughout

## Directory Structure

```
PDF EDITOR/
├── CLAUDE.md                          # Project memory
├── CONVERSION_STATUS.md               # Progress tracking
├── Operative Dictations...pdf         # Source PDF
├── chapter_{N}_raw.txt                # Raw extracted text
├── chapter_{N}_formatting.json        # Formatting metadata
├── chapter_{N}_conversion_summary.md  # Conversion notes
├── EDOP{NAME}.txt                     # Final templates
├── analyze_pdf.py                     # Analysis scripts
├── extract_chapter_{N}.py             # Extraction scripts
└── .claude/
    ├── commands/                      # Slash commands
    └── agents/                        # Subagents
```

## Slash Commands

| Command | Usage | Description |
|---------|-------|-------------|
| `extract-chapter` | `/project:extract-chapter 42 158 159` | Extract chapter 42, pages 158-159 |
| `convert-chapter` | `/project:convert-chapter 42` | Convert extracted chapter to EDOP |
| `inventory-chapters` | `/project:inventory-chapters` | List all available chapters |
| `full-conversion` | `/project:full-conversion 42` | End-to-end extraction and conversion |

## Subagents

| Agent | Description |
|-------|-------------|
| `verify-conversion` | Validates EDOP template against UVA schema |

## Safety Rules

1. **Never modify the source PDF**
2. **Preserve all SmartLink formats exactly** (@ and {} syntax)
3. **Use Python for text processing** (not bash regex)
4. **Create backups before batch operations**
5. **Verify file counts before/after operations**

## Troubleshooting

### Common Issues

| Issue | Cause | Fix |
|-------|-------|-----|
| Missing italic formatting | PDF extraction issue | Check formatting.json, re-extract |
| Wrong section boundaries | Intro material included | Adjust extraction start point |
| SmartLink not recognized | Wrong format | Use exact @NAME@ or {LIST:ID} syntax |
| Missing Findings | Source lacks section | Create from Description content |

### Debug Commands

```bash
# View formatting patterns in extracted chapter
cat chapter_42_formatting.json | python -m json.tool | grep -A2 '"italic": true'

# Count fill points in template
grep -c '\*\*\*' EDOPSMALLBOWELRESECTION.txt

# Verify all SmartLinks
grep -o '@[A-Z]*@' EDOPSMALLBOWELRESECTION.txt | sort | uniq
```

## Resources

- UVA Epic SmartLink Reference (internal)
- "Operative Dictations in General and Vascular Surgery" PDF
- Chapter index: Pages 1-8 of PDF
