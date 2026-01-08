Inventory available chapters in the PDF

## Process

1. Read the PDF table of contents (typically pages 1-8)
2. List all chapters with:
   - Chapter number
   - Title
   - Page range
   - Category (Esophageal, Gastric, Small Bowel, etc.)

3. Cross-reference with `CONVERSION_STATUS.md` to show:
   - Completed conversions
   - In-progress conversions
   - Not yet started

## Output Format

```
=== PDF Chapter Inventory ===

## Completed (5)
- [x] Ch 23: Laparoscopic Plication of Perforated Ulcer (pp 100-102) → EDOPPERFORATEDPEPTICULCERLAPPLICATION
- [x] Ch 42: Laparoscopic Enterolysis for SBO (pp 158-159) → EDOPSMALLBOWLOBSTRUCTIONLAPLYSIS
...

## In Progress (0)
(none)

## Available by Category

### Esophageal (Chapters 1-17)
- [ ] Ch 1: Title (pp X-Y)
- [ ] Ch 2: Title (pp X-Y)
...

### Gastric (Chapters 18-36)
- [ ] Ch 18: Title (pp X-Y)
...

### Small Bowel (Chapters 37-46)
...

### Colorectal (Chapters 47-92)
...

### Hepatobiliary (Chapters 93-121)
...

### Hernia (Chapters 122-135)
...

### Breast (Chapters 136-153)
...

### Endocrine (Chapters 154-167)
...

### Vascular (Chapters 185-278)
...

Total: 277 chapters
Completed: 5 (1.8%)
Remaining: 272
```

## Suggested Next Conversions

Based on surgical training priorities, suggest:
1. Common emergency procedures
2. Core general surgery procedures
3. Procedures with complex decision trees (good for template logic refinement)
