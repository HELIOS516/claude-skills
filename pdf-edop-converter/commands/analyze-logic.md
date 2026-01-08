Analyze surgical decision tree for chapter: $ARGUMENTS

Argument: CHAPTER_NUMBER (must have been extracted first)
Example: `/project:analyze-logic 42`

## Purpose

Before converting, map out the complete surgical decision tree to ensure the template captures all logical paths correctly.

## Analysis Steps

### 1. Identify Indication Variants
What different conditions/indications might lead to this procedure?
- List each indication
- Note how findings differ by indication
- Note how technique varies by indication

### 2. Map Major Branch Points
What are the "choose one" decision points?
- Entry techniques (Veress vs Hasson vs Open)
- Repair techniques (Stapled vs Hand-sewn, Primary vs Mesh)
- Approach (Laparoscopic vs Open vs Robotic)

### 3. Identify Conditionals
What content only applies in certain situations?
- Gender-specific (IF MALE, IF FEMALE)
- Indication-specific (IF ISCHEMIA, IF TUMOR)
- Finding-specific (IF BOWEL INJURY, IF ADHESIONS)
- Optional adjuncts (IF EPIDURAL, IF DRAIN)

### 4. List Inline Choices
What are the small pick-one options within flowing text?
- Suture types
- Instrument names
- Locations
- Measurements

### 5. Document Decision Flow

Create a flowchart:
```
PROCEDURE
├── INDICATION determines...
│   ├── Findings to document
│   └── Technique modifications
├── ENTRY TECHNIQUE (branch)
│   ├── Option A
│   ├── Option B
│   └── Option C
├── CORE PROCEDURE (linear)
├── TECHNIQUE VARIANT (branch)
│   ├── Option A
│   └── Option B
└── CLOSURE (mostly linear)
```

## Output

Create `chapter_{N}_logic_map.md` with:
1. Indication variants
2. Branch point list
3. Conditional list
4. Decision flowchart
5. Recommended template structure

## Why This Matters

- Ensures template captures ALL valid surgical paths
- Prevents mixing incompatible techniques
- Makes the template match how surgeons actually think
- Results in a template that is actually usable, not just technically complete
