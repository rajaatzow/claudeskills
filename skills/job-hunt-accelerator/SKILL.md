---
name: job-hunt-accelerator
description: >
  Career coaching tool for software engineers and DevOps engineers job hunting in the US.
  Use this skill whenever a client wants to find jobs, search for software or DevOps positions,
  match their resume to job postings, generate cover letters, or get a tailored resume PDF.
  Trigger on phrases like "find me jobs", "search for jobs", "job hunt", "job search",
  "match my resume", "apply to jobs", "software engineer jobs", "devops jobs", "career search",
  or any time a client uploads a resume and wants to find matching positions.
  Also trigger if the user mentions Dice, Indeed, cover letter, or resume tailoring in a job-search context.
compatibility: "Requires Indeed and Dice MCP connectors, bash_tool, present_files. Python libs: reportlab, pypdf, pdfplumber."
---

# Job Hunt Accelerator

A skill for career coaches supporting software engineers and DevOps engineers in the US job market.

## Overview

This skill walks a client through a complete job search session:
1. Collect resume + target job description
2. Extract keywords and role signals
3. Search Dice (3-day filter) + Indeed in parallel
4. Score each job for resume match
5. Show only ≥80% required-skills matches
6. For approved jobs: generate tailored cover letter + rewritten resume bullets + PDF resume
7. Deliver downloadable application package

---

## Step 1 — Resume Intake

Ask the client:

> "To get started, I need your resume. You can either:
> - **Upload a file** (PDF or Word doc), or
> - **Pull from your Indeed account** if you have one linked"

**If Indeed account:** call `Indeed:get_resume`. If it returns valid content, confirm with the client that the resume looks right before proceeding.

**If file upload:** use `pdfplumber` or text extraction to read the resume content. See the pdf skill for extraction patterns.

**If both are available:** ask the client which they prefer to use as the base.

Store the full resume text as `resume_text`.

---

## Step 2 — Target Job Description Intake

Ask:

> "Now paste or upload the job description for your ideal role — the one that feels like a perfect fit. This helps me understand exactly what you're targeting."

Store as `target_jd_text`.

---

## Step 3 — Keyword & Signal Extraction

Analyze both documents and extract:

```
From resume_text:
- Primary role titles (e.g., "Senior DevOps Engineer", "Staff SWE")
- Tech stack: languages, frameworks, cloud platforms, tools, certifications
- Years of experience (infer from dates)
- Industries/domains
- Location / remote preference (if stated)

From target_jd_text:
- Role title and seniority level
- Required skills (mark as REQUIRED)
- Nice-to-have skills (mark as PREFERRED)
- Employment type (full-time, contract, etc.)
- Remote/hybrid/on-site preference
- Location
```

Use these extractions to build `search_keywords` — a prioritized list of 3–5 search terms combining role + top skills (e.g., "Senior DevOps Engineer Kubernetes AWS", "Platform Engineer Terraform").

---

## Step 4 — Job Search (72-Hour Lock)

Search both platforms using `search_keywords`. Run searches in sequence (Dice first, then Indeed).

**Critical rule: ONLY search for jobs posted in the past 72 hours. Do NOT broaden the search if results are sparse.**

### Dice Search
Use `Dice:search_jobs` for each keyword variant:
- `posted_date: "THREE"` (3-day filter — exactly 72 hours, the closest available)
- `location`: use client's preferred location or "remote"
- `employment_types`: match client preference
- `jobs_per_page`: 10–15 per query

### Indeed Search
Use `Indeed:search_jobs` for each keyword variant:
- `country_code: "US"`
- `location`: client's preferred location or "remote"
- `date_posted: "3"` or equivalent (limit to past 72 hours, if parameter available)
- **Note:** If Indeed has no date filter, you MUST post-filter results to only include jobs posted ≤72 hours ago

### Handling Empty Results

**If searches return 0 results:**
- Report to client: "**No matching jobs found from within the past 72 hours.**"
- Do NOT retry with broader search criteria (e.g., drop seniority level, remove keywords)
- Do NOT search older jobs
- This is the correct behavior — the job market may be slow today

**If searches return results but none meet the ≥80% threshold:**
- Report to client: "Searched [X] jobs from past 72 hours. [Y] had salary data. 0 met your skill requirements."
- Still do NOT broaden the search window

Deduplicate results by job title + company name. Expect 0–40 total raw results; it's normal for sparse markets.

---

## Step 4b — Compensation Data Extraction

For each job result from both Dice and Indeed, extract compensation data:

### From Dice Results
- Dice job results typically include salary range in the job listing
- Extract as: `min_salary` and `max_salary` (numeric values, e.g., 150000, 180000)
- If salary is not listed, leave compensation fields blank

### From Indeed Results
- Call `Indeed:get_job_details` using the job ID to fetch full compensation data
- Indeed may include salary ranges in the job details payload
- Extract as: `min_salary` and `max_salary` (numeric values)
- If salary is not provided, leave compensation fields blank

### Format for gSheet Column J
Format compensation as a single range string: `"$150k-$180k"` (rounded to nearest $5k)
- If only a single salary value exists, use that value alone (e.g., `"$160k"`)
- If no compensation data is available for either platform, **leave the cell blank**

Store this data for later sync to gSheet column J during Step 7 (Sync to Tracker).

---

## Step 5 — Resume Match Scoring

For each job result, score it against `resume_text`. See `references/scoring.md` for the full scoring rubric.

**Key rule: Only surface jobs where the resume contains ≥80% of the job's REQUIRED skills.**

This is the critical inversion: We ask "Does this candidate have what the job needs?" not "Does this job match the candidate's skills?"

For each job above the threshold, prepare:
```
- Job title + company
- Location / remote status
- Posted date (flag if unknown for Indeed results)
- Apply URL
- Match score: XX% of job's required skills found in resume
- Match summary: 2 sentences — which required skills align, which are missing
- Source: Dice or Indeed
```

---

## Step 6 — Client Review

Present matched jobs in a clean numbered list. Example format:

```
🟢 1. Senior DevOps Engineer — Stripe (Remote)
   📊 Match: 94% required skills
   ✅ Strong: Kubernetes, Terraform, AWS, CI/CD pipelines
   ⚠️  Gap: No Vault/secrets management mentioned on resume
   📅 Posted: 2 days ago (Dice)
   🔗 Apply: [link]

   Pursue this one? (yes / skip / more info)
```

Wait for client to respond to each job before moving on. Accept: yes, skip, more info, or stop.

If client says "more info" — call `Indeed:get_job_details` or fetch the Dice details URL for the full description.

---

## Step 7 — Sync to Tracker gSheet

Before generating application packages, sync approved jobs to your tracking gSheet. This keeps a historical record of all job searches across sessions.

See `references/gsheet-sync.md` for the full implementation guide, deduplication logic, error handling, and code examples.

### Quick Summary

**If gSheet is accessible:**
1. Fetch existing data and check for duplicates (by company + fuzzy job title match)
2. Update match scores if a job appears multiple times
3. **Include compensation data:** Sync column J with salary range (e.g., `"$150k-$180k"`) extracted in Step 4b; leave blank if unavailable
4. **Include source:** Populate column K with "Dice" or "Indeed" for each job
5. Append new jobs in batch
6. Done — data is synced

**If gSheet is not accessible:**
1. Create a new gSheet in your Drive folder with the job tracker template
2. Share with your team (`911wbo@gmail.com`, `adamwangsde@gmail.com`)
3. Send email notification with the link
4. Append today's approved jobs (including columns J and K for compensation and source)
5. Return shareable link to the client

### Why This Matters

Tracking jobs across sessions prevents duplicate pursuit, shows patterns in job market (which companies, roles, stack keep appearing), and gives you historical data for negotiation prep and timing.

---

## Step 8 — Application Package Generation

For each approved job, generate a complete application package. See `references/application-package.md` for detailed instructions on:
- Cover letter structure and tone
- Resume bullet rewriting rules
- PDF resume template and generation code

Deliver one downloadable PDF per job:
- `[ClientName]_[CompanyName]_[Role]_Resume.pdf`
- `[ClientName]_[CompanyName]_CoverLetter.pdf` (or embed in resume PDF as page 2 — ask client preference once)

Use `present_files` to share all generated files at the end.

### Apply Links Summary

After delivering all PDFs, always end the session with a consolidated list of apply URLs for every approved job. This gives the client a single reference point so they don't have to scroll back through the conversation. Format:

```
## Ready to Apply

1. Senior DevOps Engineer — Stripe (Remote)
   🔗 [Apply here](https://...)

2. Platform Engineer — Acme Corp (San Francisco, CA)
   🔗 [Apply here](https://...)
```

Include the job title, company, and location for each entry so the client can match each link to the correct application package.

---

## Scope Limits (enforce these)

- US job searches only
- No automated application submission — provide apply links, client submits themselves
- Dice: 3-day recency filter (closest to 48 hours available)
- Indeed: no date filter available; always show and flag the posting date
- Cover letter + tailored bullet rewrites only — not a full resume rewrite
- Only surface jobs with ≥80% required skills match

---

## Error Handling

- **Dice returns 0 results**: Try broader keyword (drop seniority level, keep role + top skill)
- **Indeed returns stale jobs**: Flag posting dates clearly; let client decide if worth pursuing
- **Resume file unreadable**: Ask client to paste resume text directly into chat
- **Indeed resume is outdated**: Ask client to confirm or upload a newer version
