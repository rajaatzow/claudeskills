# Quick Reference: Three Critical Fixes

## 1. Salary Data Extraction (FIXED ✅)

**What was broken:** Adam's daily run scored jobs but didn't pull salary data.

**What's fixed:**
```
DICE:  Extract from job listing → "$150k-$180k"
INDEED: Call get_job_details() → Extract salary → "$150k-$180k"
Result: Column J = Compensation, Column K = Source
Empty? Leave Column J blank (don't estimate)
```

**File:** `references/gsheet-sync.md` → See "Step 3: Salary Data Extraction"

---

## 2. 72-Hour Search Lock (FIXED ✅)

**What was broken:** Skill would broaden searches if results were sparse.

**What's fixed:**
```
Dice: posted_date = "THREE" (72 hours exactly)
Indeed: Filter to ≤72 hours old
0 results? → Report "No matching jobs found from within the past 72 hours."
DON'T broaden search. DON'T search older jobs. STOP.
```

**File:** `SKILL.md` → See "Step 4 — Job Search (72-Hour Lock)"

---

## 3. Inverted Scoring Logic (FIXED ✅)

**What was wrong:** Checking if job matches resume's skills.

**What's correct:**
```
Resume has ≥80% of JOB's REQUIRED skills? YES → Show it. NO → Skip it.

Example:
  Job needs: [Java, Spring, Microservices, Docker, SQL, AWS]
  Resume has: [Java ✓, Spring ✓, Docker ✓, SQL ✓, AWS ✓, Kubernetes]
  Result: 5/6 = 83% ✅ PASS
```

**File:** `references/scoring.md` → See "Core Principle" + "Step 3: Calculate Match Percentage"

---

## Files You Have

```
job-hunt-accelerator/
├── SKILL.md                           ← Updated skill definition
└── references/
    ├── scoring.md                     ← Inverted logic (Fix #3)
    ├── gsheet-sync.md                 ← Salary extraction (Fix #1)
    └── application-package.md         ← Cover letters + resume rewrites
```

---

## What to Do Now

1. **Copy the entire folder** to replace your old job-hunt-accelerator skill
2. **Run Adam's daily job hunt** — salary data should now populate in columns J & K
3. **Review the reference files** if you need to understand implementation details
4. **Test:** Verify salary appears, 72-hour lock holds, scoring is inverted

---

## Why It Works Now

- **Salary:** Complete working code for extraction + formatting
- **72-hour lock:** Explicit guidance to NEVER broaden searches
- **Scoring:** Clear inversion — resume must have job's required skills

All three broken pieces are now implemented and documented.
