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
5. Show only ≥90% required-skills matches
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

## Step 4 — Job Search

Search both platforms using `search_keywords`. Run searches in sequence (Dice first, then Indeed).

### Dice Search
Use `Dice:search_jobs` for each keyword variant:
- `posted_date: "THREE"` (3-day filter — closest to 48 hours available)
- `location`: use client's preferred location or "remote"
- `employment_types`: match client preference
- `jobs_per_page`: 10–15 per query

### Indeed Search
Use `Indeed:search_jobs` for each keyword variant:
- `country_code: "US"`
- `location`: client's preferred location or "remote"
- Note: **Indeed has no date filter** — flag posting date for each result when shown to client

Deduplicate results by job title + company name. Aim for 20–40 total raw results before scoring.

---

## Step 5 — Resume Match Scoring

For each job result, score it against `resume_text`. See `references/scoring.md` for the full scoring rubric.

**Key rule: Only surface jobs with a Required Skills Match ≥ 90%.**

For each job above the threshold, prepare:
```
- Job title + company
- Location / remote status
- Posted date (flag if unknown for Indeed results)
- Apply URL
- Match score: XX% required skills match
- Match summary: 2 sentences — what aligns, what's missing/gap
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

## Step 7 — Application Package Generation

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
- Only surface jobs with ≥90% required skills match

---

## Error Handling

- **Dice returns 0 results**: Try broader keyword (drop seniority level, keep role + top skill)
- **Indeed returns stale jobs**: Flag posting dates clearly; let client decide if worth pursuing
- **Resume file unreadable**: Ask client to paste resume text directly into chat
- **Indeed resume is outdated**: Ask client to confirm or upload a newer version
