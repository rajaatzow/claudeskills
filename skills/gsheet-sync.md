# gSheet Sync Implementation Guide (Career Pages)

## Overview

Sync approved jobs from company career pages to a tracking gSheet with all metadata including salary data (Column J) and source (Column K). This creates a referral-based pipeline and prevents duplicate pursuit across sessions.

---

## gSheet Structure (Expected Columns)

| Col | Header | Format | Notes |
|-----|--------|--------|-------|
| A | Date | YYYY-MM-DD | Job search date |
| B | Job Title | Text | Full job title |
| C | Company | Text | Company name |
| D | Location | Text | City, State or Remote |
| E | Match % | 0–100 | Resume match score (≥80% only) |
| F | Apply URL | Hyperlink | Direct apply link from career page |
| G | Source | Referral / Career Page | "Referral" for all career page jobs |
| H | Posted Date | YYYY-MM-DD or "N/A" | Job posting date (if available on page) |
| I | Job Description | Text | 1–2 sentence summary OR full description |
| J | Compensation | $150k-$180k | Salary range or single value; blank if N/A |
| K | Compensation Source | Career Page | Source of salary data |

---

## Step-by-Step Implementation

### 1. Connect to gSheet

Assume the tracking gSheet is accessible via Google Sheets API or Claude in Chrome extension.

```python
# Pseudocode: Connect and fetch existing data
def fetch_existing_jobs(sheet_url, sheet_name="Job Tracker"):
    """
    Fetch all existing rows from gSheet to prevent duplicates.
    Returns: List of dicts with {company, job_title, date_added, ...}
    """
    # Use Google Sheets API or Chrome extension to read all rows
    existing_jobs = []
    for row in sheet.read_all_rows():
        if row['Company'] and row['Job Title']:  # Skip empty rows
            existing_jobs.append({
                'company': row['Company'].strip(),
                'job_title': row['Job Title'].strip(),
                'date_added': row['Date'],
                'url': row['Apply URL']
            })
    return existing_jobs
```

### 2. Deduplication Logic

Compare search results against existing jobs to avoid duplicates.

```python
def is_duplicate(career_page_job, existing_jobs):
    """
    Check if a job from today's career page search already exists in the sheet.
    Uses fuzzy matching on company + job title.
    """
    from difflib import SequenceMatcher
    
    search_company = career_page_job['company'].lower().strip()
    search_title = career_page_job['job_title'].lower().strip()
    
    for existing in existing_jobs:
        existing_company = existing['company'].lower().strip()
        existing_title = existing['job_title'].lower().strip()
        
        # Company exact match (high confidence)
        company_match = search_company == existing_company
        
        # Job title fuzzy match (>85% similar)
        title_similarity = SequenceMatcher(None, search_title, existing_title).ratio()
        title_match = title_similarity > 0.85
        
        if company_match and title_match:
            return True  # Duplicate found
    
    return False
```

### 3. Salary Data Extraction (Career Pages)

**Key difference from job boards:** Career pages may not always have salary data visible.

```python
def extract_salary_from_career_page(job_posting_html):
    """
    Extract salary from a career page job posting.
    Career pages vary widely in salary transparency.
    """
    
    import re
    
    # Look for salary patterns in the posting
    # Common patterns: "$150k-$180k", "$150,000-$180,000", "Salary: $160k"
    
    salary_text = job_posting_html  # Could be from description, or dedicated field
    
    # Match patterns like: $150k, $150,000, $150K
    pattern = r'\$[\d,]+[kK]?'
    matches = re.findall(pattern, salary_text)
    
    if len(matches) >= 2:
        min_sal = normalize_salary(matches[0])  # Returns int: 150000
        max_sal = normalize_salary(matches[1])  # Returns int: 180000
        
        # Format as "$150k-$180k"
        formatted = f"${min_sal//1000}k-${max_sal//1000}k"
        return formatted, "Career Page"
    
    elif len(matches) == 1:
        sal = normalize_salary(matches[0])
        formatted = f"${sal//1000}k"
        return formatted, "Career Page"
    
    # No salary data found
    return None, None

def normalize_salary(salary_str):
    """
    Convert "$150k" or "$150,000" to integer 150000
    """
    import re
    
    # Remove $ and commas
    cleaned = salary_str.replace('$', '').replace(',', '').lower()
    
    # If it ends with 'k', multiply by 1000
    if cleaned.endswith('k'):
        return int(float(cleaned[:-1]) * 1000)
    else:
        return int(float(cleaned))
```

### 4. Build Rows to Append

```python
def prepare_rows_for_sheet(approved_jobs, resume_match_scores):
    """
    Transform approved jobs from career pages into gSheet rows.
    approved_jobs: List of job dicts from career page scraping
    resume_match_scores: Dict mapping job_id -> {match_pct, matched_skills, gap_skills}
    """
    
    from datetime import date
    
    rows = []
    today = date.today().isoformat()  # YYYY-MM-DD
    
    for job in approved_jobs:
        job_id = job['id']
        match_data = resume_match_scores.get(job_id, {})
        
        # Extract salary from career page job posting
        compensation, comp_source = extract_salary_from_career_page(job['job_description'])
        
        # Build row
        row = {
            'Date': today,
            'Job Title': job['title'],
            'Company': job['company'],
            'Location': job['location'],
            'Match %': f"{match_data.get('match_percentage', 'N/A')}%",
            'Apply URL': job['apply_url'],
            'Source': 'Referral',  # Always "Referral" for career page jobs
            'Posted Date': job.get('posted_date', 'N/A'),
            'Job Description': job.get('description_summary', ''),
            'Compensation': compensation if compensation else '',  # Blank if no data
            'Compensation Source': comp_source if comp_source else ''  # Blank if no data
        }
        
        rows.append(row)
    
    return rows
```

### 5. Append Rows to gSheet (Batch Operation)

```python
def append_rows_to_sheet(sheet_url, sheet_name, rows):
    """
    Append new rows to gSheet in batch.
    Uses Google Sheets API batch write or Chrome extension for bulk insert.
    """
    
    # Find the next available row
    last_row = find_last_row(sheet_url, sheet_name)
    start_row = last_row + 1
    
    # Build batch write request
    values = []
    for row in rows:
        values.append([
            row['Date'],
            row['Job Title'],
            row['Company'],
            row['Location'],
            row['Match %'],
            row['Apply URL'],
            row['Source'],
            row['Posted Date'],
            row['Job Description'],
            row['Compensation'],  # Column J
            row['Compensation Source']  # Column K
        ])
    
    # Write to sheet
    # Option A: Google Sheets API
    # service.spreadsheets().values().batchUpdate(
    #     spreadsheetId=sheet_id,
    #     body={
    #         'data': [{
    #             'range': f"{sheet_name}!A{start_row}",
    #             'values': values
    #         }]
    #     }
    # ).execute()
    
    # Option B: Claude in Chrome (manual entry if API unavailable)
    for i, value_row in enumerate(values):
        current_row = start_row + i
        for col_idx, value in enumerate(value_row):
            cell_ref = f"{chr(65 + col_idx)}{current_row}"  # A1, B1, etc.
            set_cell_value(cell_ref, value)
    
    return len(rows)  # Number of rows added
```

### 6. Error Handling

```python
def sync_to_sheet_with_error_handling(sheet_url, career_page_jobs, resume_match_scores):
    """
    Main sync function with robust error handling.
    """
    
    try:
        # Step 1: Fetch existing jobs
        existing_jobs = fetch_existing_jobs(sheet_url)
    except Exception as e:
        print(f"ERROR: Could not fetch existing jobs from sheet: {e}")
        print("PROCEEDING without deduplication — manually verify no duplicates are added")
        existing_jobs = []
    
    # Step 2: Filter for approved jobs (≥80% match)
    approved = [job for job in career_page_jobs 
                if resume_match_scores[job['id']].get('match_percentage', 0) >= 80]
    
    # Step 3: Dedup
    approved_to_add = [job for job in approved 
                       if not is_duplicate(job, existing_jobs)]
    
    if not approved_to_add:
        print("No new jobs to add (all are duplicates or below threshold)")
        return 0
    
    # Step 4: Extract salary data
    # CRITICAL: Ensure this is called for every job
    rows = []
    for job in approved_to_add:
        try:
            prepared_row = prepare_rows_for_sheet([job], resume_match_scores)
            rows.extend(prepared_row)
        except Exception as e:
            print(f"WARNING: Failed to prepare row for {job['company']} {job['title']}: {e}")
            # Continue with other jobs, don't fail entire sync
            continue
    
    # Step 5: Write to sheet
    try:
        rows_added = append_rows_to_sheet(sheet_url, rows)
        print(f"✅ Successfully added {rows_added} jobs to sheet")
        return rows_added
    except Exception as e:
        print(f"ERROR: Failed to write to sheet: {e}")
        print("Jobs were scored but NOT added to sheet. Try again manually or check sheet access.")
        return 0
```

---

## Key Points for Career Page Salary Extraction

1. **Salary may not be visible** on all career pages — some are transparent, some are not
2. **Salary is NOT a filter** — if a job matches ≥80%, include it regardless of salary availability
3. **Always leave blank if no data** — don't estimate or use industry averages
4. **Column J (Compensation)** — Only populate if extracted from page; otherwise blank
5. **Column K (Compensation Source)** — "Career Page" if salary found; blank if not

---

## Testing Checklist

- [ ] Career page jobs are scraped and extracted successfully
- [ ] Salary extracted from career pages where available (e.g., "$150k-$180k")
- [ ] Rows append to gSheet with columns J (Compensation) and K (Source) populated
- [ ] If no salary on page: columns J & K are blank (not "N/A", not estimated)
- [ ] Deduplication works: same job scraped from page twice doesn't add duplicate rows
- [ ] Match score ≥80% only: no jobs below 80% appear in sheet
- [ ] Source column shows "Referral" for all career page jobs
- [ ] Date column shows today's date for all new entries

---

## Differences from job-hunt-accelerator gSheet Sync

| Aspect | job-hunt-accelerator | job-hunt-referrals |
|--------|---------------------|-------------------|
| **Source** | Dice / Indeed | Career Page / Referral |
| **Salary API calls** | Dice search, Indeed get_job_details | Career page HTML parsing only |
| **Salary availability** | Expected on both platforms | May be missing on some pages |
| **Date filter** | 72-hour mandatory lock | No date filter (all jobs on page) |
| **Dedup logic** | Same (company + title fuzzy match) | Same |
| **Compensation Source (K)** | "Dice" or "Indeed" | "Career Page" |

---

## Why Salary Data for Career Pages Matters

- **Transparency:** You build a salary database of target companies' postings
- **Negotiation prep:** Historical salary data helps prepare for offer discussions
- **Pipeline visibility:** Track which companies are offering what compensation for your role
- **No scrambling:** When you interview, you already know ballpark range

Even if not all postings have salary, what's visible provides valuable market data.
