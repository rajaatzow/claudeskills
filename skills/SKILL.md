---
name: job-hunt-referrals
description: >
  Find high-matched job opportunities across custom referral career pages.
  Use this skill whenever a user wants to search for jobs on specific company career sites,
  match a resume to job postings across multiple career pages, or populate a job tracking
  Google Sheet with matched opportunities. Trigger on phrases like "search these companies",
  "find jobs on career pages", "match my resume to referral jobs", "populate job tracker",
  or when a user provides both a resume and a list of company career URLs.
  Best for coordinated job searches across target companies with automatic gSheet syncing.
compatibility: "Requires Claude in Chrome (browser automation), Google Sheets API access (for gSheet population), python-docx or pdfplumber for resume parsing, bash_tool."
---

# Job Hunt Referrals

A skill for finding and tracking high-matched job opportunities across custom company career pages with automatic Google Sheet syncing.

## Overview

This skill walks through a complete job search session across custom referral career pages:
1. Collect resume (file upload)
2. Extract keywords, skills, seniority level, geography
3. Navigate and scrape target company career pages (Claude in Chrome)
4. Extract job postings and requirements
5. Extract salary data from career pages (if available)
6. Score each job for resume match (resume has ≥80% of job's **required** skills)
7. Sync approved/matched jobs to Google Sheet with source "Referral"
8. Deliver summary of matched opportunities

**Key difference from job-hunt-accelerator**: This skill targets specific company career pages instead of broad job boards (Indeed/Dice), uses Claude in Chrome for web scraping instead of API searches, and focuses on building a referral-based opportunity pipeline. **Same scoring logic:** Resume must contain ≥80% of the job's required skills (not vice versa).

---

## Step 1 — Resume Intake

Ask the user:

> "To get started, I need your resume. Please upload a PDF or Word doc (.docx)."

Extract resume content using Python docx or pdfplumber. Store as `resume_text`.

**Resume extraction should capture:**
- Full name, email, phone
- Current/most recent role and seniority level
- Technical skills (languages, frameworks, tools, platforms)
- Years of experience
- Key domains/industries
- Location or remote preference (if stated)

---

## Step 2 — Target Company Career Pages

The user should provide (or you should confirm):
- List of target company career page URLs
- Expected job count per company (varies by company size)
- Geographic preference (US, remote, specific region)

**Default career pages for referral job search:**
- https://plaid.com/careers/
- https://www.collective.com/careers
- https://www.purestorage.com/company/careers/opportunities.html
- https://careers.google.com/jobs
- https://www.uber.com/us/en/careers/list/
- https://www.pepsicojobs.com/main/jobs?page=1&keywords=technology&sortBy=relevance&country=USA%7CUnited%20States
- https://www.criticalmass.com/jobs
- https://entrustsol.com/careers/
- https://recruiting2.ultipro.com/ENE1003ENENG/JobBoard/020a913b-6f29-4cb0-a3b2-8b9d82d26c72/?q=&o=postedDateDesc
- https://www.trajector.com/careers/
- https://job-boards.greenhouse.io/scoutmotors?keyword=Software
- https://www.whiting-turner.com/careers/apply/
- https://block.xyz/careers/jobs
- https://www.rallyhealth.com/careers
- https://careers.hcahealthcare.com/
- https://www.issi.com/US/career-opportunities-open.shtml
- https://www.invaluable.com/inv/about-us/careers/?srsltid=AfmBOorwaQWSN_hHjd3T_7YmtLh6UOKAP6SkqIzrWsdKImTKuRy1yC6h
- https://careers.sf.gov/
- https://www.levels.fyi/companies/highlander-partners/jobs?from=company_header_navbar
- https://www.atlassian.com/company/careers/all-jobs
- https://careers.cognizant.com/us-en/jobs/
- https://careers.adobe.com/us/en
- https://careers.intuitive.com/en/jobs/

---

## Step 3 — Keyword & Signal Extraction

From `resume_text`, extract:

```
- Role title(s): "Backend Engineer", "Senior SWE", etc.
- Tech stack: Python, Java, Kafka, AWS, microservices, Spring Boot, etc.
- Years of experience: Parse from dates
- Seniority inference: Junior (0-2yr), Mid (3-6yr), Senior (7-10yr), Staff (10+yr)
- Domain expertise: payments, infrastructure, high-scale systems, etc.
- Location: infer from resume if present
```

Use these to build a **matching profile**:
```json
{
  "role_level": "Senior",
  "core_skills": ["Java", "Python", "Kafka", "AWS", "Microservices"],
  "preferred_skills": ["Spring Boot", "MySQL", "REST APIs", "System Design"],
  "years_experience": 6,
  "domain": ["backend", "payments", "infrastructure"],
  "geography": ["Bay Area", "Remote"]
}
```

This profile drives resume-to-job matching logic.

---

## Step 4 — Job Search via Career Pages (Claude in Chrome)

For each target career page URL:

1. **Navigate** to the URL using Claude in Chrome
2. **Extract all visible job postings** from the page:
   - Job title
   - Job description / requirements
   - Location
   - Job URL (direct apply link if available)
   - Seniority level (if stated)
   - Department/team (if available)

3. **Handle pagination**: If the site has pagination, navigate through pages to collect all visible jobs

4. **Store extracted data** in a structured format:
   ```json
   {
     "company": "Plaid",
     "job_title": "Senior Backend Engineer",
     "job_url": "https://...",
     "location": "San Francisco, CA",
     "seniority": "Senior",
     "required_skills_raw": "Java, Python, Kafka, AWS, system design, high-scale systems",
     "job_description": "..."
   }
   ```

**Browser automation tips:**
- Use `Claude in Chrome:read_page` to parse job listing elements
- Use `Claude in Chrome:find` to locate job cards/sections
- Click "View More" / "Load More" buttons as needed
- Handle different career page layouts (greenhouse, lever, custom sites)
- Set a reasonable timeout per page (30-60 seconds)

---

## Step 4b — Salary Data Extraction

For each job extracted from career pages, attempt to extract compensation data:

### Salary Data Sources on Career Pages

Career pages vary in salary transparency:
- **Some display salary ranges** in the job posting (e.g., "$150k-$180k")
- **Some hide salary until application** ("Salary: Competitive" or no mention)
- **Some link to a separate compensation page**

### Extraction Process

1. **Check job posting for explicit salary** — Look in job description for salary range pattern: `$XXXk-$XXXk`, `$XXX,XXX-$XXX,XXX`, etc.
2. **Parse the salary range** — Use regex to find min/max values
3. **Format standardized** — Convert to `"$150k-$180k"` format (rounded to nearest $5k)
4. **If no explicit salary found** — Leave compensation field blank (don't assume or estimate)

### Format for gSheet Column J

```
Format: "$150k-$180k" (if range available)
Format: "$160k" (if single value available)
Blank: If no salary data found on page
```

### Common Career Page Salary Patterns

**Explicit salary:**
```
"Salary: $150,000 - $180,000"
"$150k - $180k per year"
"Compensation: $160,000 - $190,000 annually"
```

**Hidden/Not listed:**
```
"Salary: Competitive" (no actual number)
"Compensation: Commensurate with experience" (no number)
No salary section visible (leave blank)
```

### Implementation

Store extracted salary as part of job data:
```json
{
  "company": "Plaid",
  "job_title": "Senior Backend Engineer",
  "job_url": "https://...",
  "location": "San Francisco, CA",
  "compensation": "$150k-$180k",  // NEW: extracted salary
  "salary_source": "Career Page",  // NEW: where salary came from
  "required_skills_raw": "Java, Python, Kafka, AWS, system design",
  "job_description": "..."
}
```

**Important:** If no salary data is found after checking the full job posting, the `compensation` field should be empty/null (not "N/A", not an estimate).

---

## Step 5 — Resume Match Scoring

For each extracted job, calculate match score using the **inverted logic** that mirrors job-hunt-accelerator.

See `references/scoring.md` for the complete rubric.

**Key principle: Only surface jobs where the resume contains ≥80% of the job's REQUIRED skills.**

This is the critical inversion: We ask "Does this candidate have what the job needs?" not "Does this job match the candidate's skills?"

**Matching logic:**
```
Required Skills from Job = Extract from job posting (from career page)
Resume Skills = Extract from resume_text
Match % = (intersection / required_skills_count) * 100
```

**Secondary scoring factors (for tie-breaking):**
- Seniority alignment: ± 5 points for level match
- Geography fit: + 10 points if location matches preference
- Domain relevance: + 5 points if domain expertise aligns

**Threshold: Only surface jobs with ≥80% required skills match.**

For each matched job, prepare:
```
- Job title + company
- Location
- Job URL (apply link)
- Match score: XX% required skills match
- Match summary: 1-2 sentences — aligned skills, any gaps
- Source: "Referral"
```

---

## Step 6 — Client Review & Selection

Present matched jobs in a clear list. Example:

```
🟢 1. Senior Backend Engineer — Plaid (San Francisco, CA)
   📊 Match: 92% required skills
   ✅ Matched: Java, Python, Kafka, AWS, Microservices, System Design
   ⚠️  Gap: None significant
   🔗 Apply: [link]
   Approve for tracking? (yes / skip)

🟢 2. Principal Engineer — Google (Remote)
   📊 Match: 85% required skills
   ✅ Matched: Java, Python, AWS, Leadership, Scale
   ⚠️  Gap: Kafka (not required but nice-to-have)
   🔗 Apply: [link]
   Approve for tracking? (yes / skip)
```

Wait for client to respond: "yes" to approve, "skip" to pass, or "more info" for full job details.

---

## Step 7 — Google Sheet Sync

Sync approved jobs to the user's Google Sheet. See `references/gsheet-sync.md` for implementation details.

### Quick Summary

**Before syncing**, confirm with the user:
- Google Sheet URL
- Target tab name (e.g., "BackendEngineer")
- Whether to deduplicate against existing entries

**Sync logic:**
1. Read existing data from gSheet tab
2. Check for duplicates (company + job title fuzzy match)
3. Append new matched jobs with:
   - Job Title
   - Company
   - Location
   - Match %
   - Job URL
   - Source: "Referral"
   - Date Added: Today
   - Notes: Match summary + any gaps
   - **Compensation (Column J):** Salary range (e.g., `"$150k-$180k"`) extracted in Step 4b; blank if unavailable
   - **Compensation Source (Column K):** "Career Page" (source of salary data)

**If gSheet access fails:**
- Offer to create a new gSheet in the user's Drive
- Populate it with matched jobs (including compensation data)
- Return shareable link

---

## Step 8 — Summary & Next Steps

After syncing, deliver a consolidated summary:

```
## Job Match Summary

Total companies scanned: 20
Total jobs found: 145
High-match jobs (≥80%): 12
Approved for tracking: 12
Source: Referral

## Next Steps

1. Review jobs in your Google Sheet: [link]
2. Prioritize by match % and company preference
3. Follow up with your referral contact at target companies
4. Schedule informational interviews
```

Provide a direct link to the gSheet tab so the client can review tracked opportunities.

---

## Scope & Limits

- **Match threshold**: 80% required skills (vs. 90% for job-hunt-accelerator)
- **Source label**: Always "Referral" for all jobs from career pages
- **Geography**: Flexible; honor client preference but surface all matches
- **No automated applications**: Provide apply links; client follows up
- **Deduplication**: Check gSheet before appending to avoid duplicate tracking

---

## Error Handling

- **Career page not accessible**: Flag the URL, try alternative access method, move to next
- **No jobs found on a page**: Note it, continue to next company
- **Resume extraction fails**: Ask client to paste resume text directly
- **gSheet access denied**: Request permission or create new sheet
- **Job listing incomplete**: Extract what's available (title + URL minimum), mark as "needs review"

---

## References

- `references/gsheet-sync.md` — Google Sheet population, authentication, deduplication
- `references/skill-matching.md` — Detailed scoring rubric and keyword matching examples
- `references/browser-scraping.md` — Claude in Chrome patterns for different career page layouts
