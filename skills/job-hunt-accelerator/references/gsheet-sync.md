# gSheet Sync Implementation Guide

## Overview

Sync approved jobs to a tracking gSheet with all metadata including salary data (Column J) and source (Column K). This creates a historical record and prevents duplicate pursuit across sessions.

---

## gSheet Structure (Expected Columns)

| Col | Header | Format | Notes |
|-----|--------|--------|-------|
| A | Date | YYYY-MM-DD | Job search date |
| B | Job Title | Text | Full job title |
| C | Company | Text | Company name |
| D | Location | Text | City, State or Remote |
| E | Match % | 0–100 | Resume match score (≥80% only) |
| F | Apply URL | Hyperlink | Direct apply link |
| G | Source | Dice / Indeed | Job platform |
| H | Posted Date | YYYY-MM-DD or "N/A" | Job posting date (may be unknown for Indeed) |
| I | Job Description | Text | 1–2 sentence summary OR full description |
| J | Compensation | $150k-$180k | Salary range or single value; blank if N/A |
| K | Compensation Source | Dice / Indeed | Which platform provided salary data |

---

## Step-by-Step Implementation

### 1. Connect to gSheet

Assume the tracking gSheet is accessible via Google Sheets API or Claude in Chrome extension.

```python
# Pseudocode: Connect and fetch existing data
def fetch_existing_jobs(sheet_id, sheet_name="Job Tracker"):
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
def is_duplicate(search_result, existing_jobs):
    """
    Check if a job from today's search already exists in the sheet.
    Uses fuzzy matching on company + job title.
    """
    from difflib import SequenceMatcher
    
    search_company = search_result['company'].lower().strip()
    search_title = search_result['job_title'].lower().strip()
    
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

### 3. Salary Data Extraction

**Critical:** Extract salary from both Dice and Indeed. This is where today's run failed for Adam Wang.

#### From Dice Results

```python
def extract_salary_from_dice(dice_job_result):
    """
    Dice job results include salary in the listing.
    Extract min/max and format as "$150k-$180k"
    """
    
    salary_text = dice_job_result.get('salary', None)
    
    if not salary_text or salary_text.strip() == '':
        return None, None, None  # No salary data
    
    # Parse salary range: e.g., "$150,000 - $180,000" or "$150k - $180k"
    import re
    
    # Match patterns like: $150k, $150,000, $150K
    pattern = r'\$[\d,]+[kK]?'
    matches = re.findall(pattern, salary_text)
    
    if len(matches) >= 2:
        min_sal = normalize_salary(matches[0])  # Returns int: 150000
        max_sal = normalize_salary(matches[1])  # Returns int: 180000
        
        # Format as "$150k-$180k"
        formatted = f"${min_sal//1000}k-${max_sal//1000}k"
        return formatted, "Dice", salary_text
    
    elif len(matches) == 1:
        sal = normalize_salary(matches[0])
        formatted = f"${sal//1000}k"
        return formatted, "Dice", salary_text
    
    return None, None, None

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

#### From Indeed Results

```python
def extract_salary_from_indeed(indeed_job_id):
    """
    Indeed doesn't always include salary in search results.
    MUST call Indeed:get_job_details to fetch full job posting.
    """
    
    # Step 1: Call Indeed:get_job_details with job_id
    job_details = call_indeed_get_job_details(job_id=indeed_job_id)
    
    # Step 2: Look for salary in job_details payload
    # Common fields: salary, salary_range, min_salary, max_salary
    
    salary_text = job_details.get('salary', None) or \
                  job_details.get('salary_range', None)
    
    if not salary_text or salary_text.strip() == '':
        return None, None, None  # No salary data in Indeed
    
    # Parse similar to Dice
    import re
    pattern = r'\$[\d,]+[kK]?'
    matches = re.findall(pattern, salary_text)
    
    if len(matches) >= 2:
        min_sal = normalize_salary(matches[0])
        max_sal = normalize_salary(matches[1])
        formatted = f"${min_sal//1000}k-${max_sal//1000}k"
        return formatted, "Indeed", salary_text
    
    elif len(matches) == 1:
        sal = normalize_salary(matches[0])
        formatted = f"${sal//1000}k"
        return formatted, "Indeed", salary_text
    
    return None, None, None
```

### 4. Build Rows to Append

```python
def prepare_rows_for_sheet(approved_jobs, resume_match_scores):
    """
    Transform approved jobs into gSheet rows.
    approved_jobs: List of job dicts from Dice/Indeed
    resume_match_scores: Dict mapping job_id -> {match_pct, matched_skills, gap_skills}
    """
    
    from datetime import date
    
    rows = []
    today = date.today().isoformat()  # YYYY-MM-DD
    
    for job in approved_jobs:
        job_id = job['id']
        match_data = resume_match_scores.get(job_id, {})
        
        # Extract salary based on source
        if job['source'] == 'Dice':
            compensation, comp_source, _ = extract_salary_from_dice(job)
        else:  # Indeed
            compensation, comp_source, _ = extract_salary_from_indeed(job['id'])
        
        # Build row
        row = {
            'Date': today,
            'Job Title': job['title'],
            'Company': job['company'],
            'Location': job['location'],
            'Match %': f"{match_data.get('match_percentage', 'N/A')}%",
            'Apply URL': job['apply_url'],
            'Source': job['source'],  # Dice or Indeed
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
def append_rows_to_sheet(sheet_id, rows, sheet_name="Job Tracker"):
    """
    Append new rows to gSheet in batch.
    Uses Google Sheets API batch write or Chrome extension for bulk insert.
    """
    
    # Find the next available row
    last_row = find_last_row(sheet_id, sheet_name)
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
def sync_to_sheet_with_error_handling(sheet_id, search_results, resume_match_scores):
    """
    Main sync function with robust error handling.
    """
    
    try:
        # Step 1: Fetch existing jobs
        existing_jobs = fetch_existing_jobs(sheet_id)
    except Exception as e:
        print(f"ERROR: Could not fetch existing jobs from sheet: {e}")
        print("PROCEEDING without deduplication — manually verify no duplicates are added")
        existing_jobs = []
    
    # Step 2: Filter for approved jobs (≥80% match)
    approved = [job for job in search_results 
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
        rows_added = append_rows_to_sheet(sheet_id, rows)
        print(f"✅ Successfully added {rows_added} jobs to sheet")
        return rows_added
    except Exception as e:
        print(f"ERROR: Failed to write to sheet: {e}")
        print("Jobs were scored but NOT added to sheet. Try again manually or check sheet access.")
        return 0
```

---

## Key Points for Adam Wang's Daily Run

1. **Salary extraction must be called for EVERY job**, not just some
2. **Dice has salary in the listing** — extract it during Step 4 (Job Search), not later
3. **Indeed requires `get_job_details` call** — don't skip this step, it's how salary data is fetched
4. **Log salary extraction failures** — if a job has no salary data, log it explicitly so we know why
5. **Leave Column J blank if no data** — don't estimate or use industry averages

---

## Testing Checklist

- [ ] Dice search returns jobs with salary data visible in result
- [ ] Extract salary from Dice: `$150k-$180k` format appears in logs
- [ ] Indeed `get_job_details` is called for each Indeed job
- [ ] Extract salary from Indeed: format matches Dice
- [ ] Rows append to gSheet with columns J (Compensation) and K (Compensation Source) populated
- [ ] If no salary: columns J & K are blank (not "N/A", not "$0", not estimated)
- [ ] Deduplication works: same job searched twice doesn't add duplicate rows
- [ ] Match score ≥80% only: no jobs below 80% appear in sheet

---

## Why Salary Was Missing in Today's Run

**Most likely causes:**
1. `get_job_details` was not called for Indeed jobs
2. Salary extraction code wasn't executed (missing from workflow)
3. Salary was extracted but not written to columns J & K
4. Error during extraction was silently caught (not logged)

**Resolution:** Ensure salary extraction is a mandatory step in the job search workflow, with explicit logging of successes and failures.
