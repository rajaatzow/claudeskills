# Instructions
I built a Cowork scheduled task for daily job hunting. Here's the generic version — fill in your resume details, create a Google Sheet, and set it up as a daily scheduled task in Cowork. It runs automatically every morning and only surfaces jobs matching your profile.

# Daily Job Hunt Automation

## Objective
Run a fully autonomous daily job search for [YOUR NAME]. Search job boards for new matching positions, score them against your resume, filter out jobs already recorded in your tracking Google Sheet, and append qualifying new matches as rows.

---

## Your Profile — Resume Summary
Use this information for scoring. Do not ask the user for a resume.

**Name:** [YOUR NAME]  
**Target role:** [TARGET JOB TITLE]  
**Experience:** [YEARS OF EXPERIENCE] in [DOMAIN/SPECIALIZATION]  
**Current employer:** [CURRENT COMPANY] — [CURRENT TITLE] ([START DATE]–Present)

**Required skills (must-have for scoring):** [SKILL 1], [SKILL 2], [SKILL 3], [SKILL 4], [SKILL 5], [SKILL 6], [SKILL 7], [SKILL 8]
*Note: List exactly 8 required skills. Each skill = 12.5% of the match score.*

**Preferred skills (nice-to-have):** [SKILL], [SKILL], [SKILL], [SKILL]

**Scoring rule:** Only surface jobs with ≥90% Required Skills Match (7+ of 8 required skills present).

---

## Step 1 — Search for Jobs

Search using both Dice and Indeed MCPs. Run searches in sequence (Dice first, then Indeed).

**Search keyword queries to use:**
- "[QUERY 1]"
- "[QUERY 2]"
- "[QUERY 3]"

*Tip: Use 3 search phrases that combine your target title, key technical skills, and domain (e.g., "Senior Software Engineer Java payments").*

**Dice search parameters:**
- `posted_date`: "THREE" (3-day filter)
- `location`: "[YOUR PREFERRED LOCATION]" (e.g., "remote", "United States")
- `jobs_per_page`: 15

**Indeed search parameters:**
- `country_code`: "US"
- `location`: "[YOUR PREFERRED LOCATION]" (e.g., "remote")

Collect all results. Internally deduplicate by job title + company name (case-insensitive). Aim for 20–40 raw results before scoring.

---

## Step 2 — Check Google Sheet for Already-Seen Jobs

Use Claude in Chrome to navigate to your Google Sheet:
`[PASTE YOUR GOOGLE SHEET URL HERE]`

Read all existing rows. Extract the "Job Title" and "Company" columns from every row (skip the header). Build a set of already-seen combinations (case-insensitive).

Remove any job from your search results whose (Job Title + Company) combination already exists in the sheet. These are duplicates — skip them entirely.

---

## Step 3 — Score Remaining Jobs

For each job not already in the sheet, score it against your resume:

**Required skills check:** For each of your 8 required skills, check if it appears in the job description (job title, description text, required qualifications).

**Score = (number of required skills found / 8) × 100%**

Only keep jobs scoring ≥90% (i.e., at least 7 of 8 required skills present).

For each qualifying job, prepare:
- **Date Found:** today's date (YYYY-MM-DD)
- **Job Title**
- **Company**
- **Location** (remote / city / hybrid)
- **Match Score** (e.g., "94%")
- **Matched Skills** (comma-separated list of required skills found in job posting)
- **Gaps** (missing required skills, if any; "None" if all 8 present)
- **Posted Date** (the date the job was posted; flag as "Unknown" if not available)
- **Apply URL** (direct link to the job posting)
- **Source** (Dice or Indeed)

---

## Step 4 — Write New Jobs to Google Sheet

Using Claude in Chrome, navigate to your Google Sheet:
`[PASTE YOUR GOOGLE SHEET URL HERE]`

**If the sheet is empty (no rows at all):** First create a header row with these columns in order:
`Date Found | Job Title | Company | Location | Match Score | Matched Skills | Gaps | Posted Date | Apply URL | Source`

Then append one row per qualifying new job at the bottom of the existing data.

**If no new qualifying jobs were found:** Add a single row with today's date in "Date Found" and "No new matches found today" in the "Job Title" column, leaving all other cells blank.

---

## Success Criteria
- Google Sheet has been updated with today's run
- Any new jobs scoring ≥90% on required skills are present as new rows
- No duplicate rows (same Job Title + Company) were added
- Task completes with a brief summary: how many jobs were searched, how many were new, how many scored ≥90%, and how many were written to the sheet

---

## Setup Instructions for Users

**To customize this for yourself:**

1. Replace all `[BRACKETED ITEMS]` with your own information:
   - `[YOUR NAME]`
   - `[TARGET JOB TITLE]`
   - `[YEARS OF EXPERIENCE]` and `[DOMAIN/SPECIALIZATION]`
   - `[CURRENT COMPANY]`, `[CURRENT TITLE]`, `[START DATE]`
   - `[SKILL 1]` through `[SKILL 8]` — list exactly 8 must-have skills
   - `[SKILL]` for preferred skills (optional, can list any number)
   - `[QUERY 1]`, `[QUERY 2]`, `[QUERY 3]` — your search phrases
   - `[YOUR PREFERRED LOCATION]` — where you want to work
   - `[PASTE YOUR GOOGLE SHEET URL HERE]` — create a Google Sheet and share the link (see below)

2. **Create your Google Sheet:**
   - Go to https://sheets.google.com and create a new blank spreadsheet
   - Share it so anyone with the link can view it (or keep it private if you prefer)
   - Copy the shareable URL and paste it into this file where indicated

3. **Set up the Cowork scheduled task:**
   - Open Claude Cowork on your desktop
   - Click "Scheduled" in the left sidebar → "+ New task"
   - **Name:** "Daily Job Hunt" (or your preferred name)
   - **Frequency:** Daily (at a time of your choosing, e.g., 8:00 AM)
   - **Description:** "Automated daily job search using Dice and Indeed"
   - **Instructions:** Copy and paste the entire text of this file (after customization) into the prompt field
   - **Model:** Claude Sonnet (recommended for balanced speed/intelligence)
   - **Working folder:** Select a folder on your computer (Claude will save logs here if needed)
   - Click "Save"

4. **Customize the search keywords:**
   - Use 3 phrases that target your role, key skills, and domain
   - Example for a backend engineer: "Senior Backend Engineer Java AWS", "Spring Boot microservices", "Payment systems engineer"

5. **Keep your profile section up-to-date:**
   - Every 6 months or when your skills/experience change, update the resume summary at the top
   - This ensures Claude scores jobs fairly based on your current profile
