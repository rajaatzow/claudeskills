# Job Hunt Referrals — Updates for Consistency with job-hunt-accelerator

## Changes Made

### 1. ✅ Added Salary Data Extraction from Career Pages

**What's new:** Step 4b — Salary Data Extraction

Career pages vary in salary transparency:
- Some display explicit salary ranges: `"$150k-$180k"`
- Some say "Competitive" (no actual number)
- Some don't mention salary at all

**Implementation:**
- Parse job posting HTML for salary patterns: `$XXXk-$XXXk` or `$XXX,XXX-$XXX,XXX`
- Format standardized: `"$150k-$180k"` (rounded to nearest $5k)
- If no salary found: Leave blank (don't estimate or assume)
- Store in job data: `compensation` field + `salary_source` field

**Sync to gSheet:**
- **Column J (Compensation):** Extracted salary range or single value
- **Column K (Compensation Source):** "Career Page" (or blank if no data)

---

### 2. ✅ Created Reference Files (Matching job-hunt-accelerator Structure)

#### `references/scoring.md`
- **Inverted scoring logic:** Resume must have ≥80% of job's REQUIRED skills
- Same principle as job-hunt-accelerator
- Adapted examples for career page jobs
- Python pseudocode for scoring function
- Secondary factors for tie-breaking (seniority, geography, domain)

#### `references/gsheet-sync.md`
- Complete implementation for syncing career page jobs to gSheet
- Salary extraction from HTML parsing (not API calls)
- Deduplication logic (company + title fuzzy match)
- Batch append to gSheet with all columns (A–K)
- Error handling with explicit logging
- Testing checklist

---

### 3. ✅ Updated Scoring Logic to Inverted Approach

**Before (unclear):**
> "Calculate match score: Required Skills Match = (matched_required_skills / total_required_skills) * 100"

**After (inverted, matching job-hunt-accelerator):**
> "Resume must contain ≥80% of the job's REQUIRED skills. We ask: 'Does this candidate have what the job needs?'"

**Updated sections:**
- Overview: Clarified same scoring logic as job-hunt-accelerator
- Step 5: Full explanation of inverted logic
- Match formula: Clear direction (job's required skills vs. resume's skills)

---

## File Structure

```
job-hunt-referrals/
├── SKILL.md                          (Updated)
└── references/
    ├── scoring.md                    (NEW: Inverted logic for career pages)
    └── gsheet-sync.md                (NEW: Salary extraction + sync for career pages)
```

---

## What's the Same as job-hunt-accelerator?

✅ Inverted scoring logic (resume has job's required skills)
✅ 80% threshold minimum
✅ Salary extraction methodology
✅ gSheet structure (columns A–K, including J=Compensation)
✅ Deduplication logic
✅ Error handling patterns

## What's Different?

- **Data source:** Career pages (scraped via Claude in Chrome) instead of job board APIs
- **Salary extraction:** HTML parsing instead of API calls
- **Date filtering:** None (all jobs on page) vs. 72-hour lock in job-hunt-accelerator
- **Source label:** "Referral" instead of "Dice" or "Indeed"

---

## Next Steps

1. **Copy the entire folder** to replace your old job-hunt-referrals skill
2. **Verify gSheet structure** has columns A–K (especially J=Compensation, K=Source)
3. **Test career page scraping** — confirm jobs are extracted with salary data where available
4. **Review the reference files** for implementation details

---

## Key Takeaway

job-hunt-referrals now has:
✅ Salary extraction from career pages (if available)
✅ Inverted scoring logic matching job-hunt-accelerator
✅ Complete reference files (scoring.md, gsheet-sync.md)
✅ Consistent gSheet structure with compensation tracking

Both skills now use the same scoring principle and track the same metadata, just from different sources (job boards vs. career pages).
