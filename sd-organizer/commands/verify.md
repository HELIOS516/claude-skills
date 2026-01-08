# Verify Command

Verify organization completed successfully.

## Usage
```
/sd-organize:verify {CHARACTER}
```

## Checks

1. **File Count**
   - Compare before/after counts
   - Flag if any files missing

2. **Naming Compliance**
   - Check all files match expected pattern
   - Flag non-compliant names

3. **Duplicates**
   - Check for duplicate filenames
   - Check for files with same hash

4. **Orphaned Files**
   - Check for files in root that should be in subfolders
   - Check for empty folders that should be cleaned

5. **Metadata Verification**
   - Sample files and verify metadata extracted correctly
   - Check for "no_metadata" or "no_tags" in names

## Output

```
=== VERIFY: SIJIA ===
File count: 303 (expected: 303) ✓
Naming compliance: 303/303 ✓
Duplicates: 0 ✓
Orphaned files: 0 ✓
Metadata quality: 100% with metadata ✓

Status: VERIFIED ✓
```
