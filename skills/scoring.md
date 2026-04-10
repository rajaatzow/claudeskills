# Resume Match Scoring Rubric (Career Pages)

## Core Principle

**A job qualifies if the resume contains 80%+ of the job's REQUIRED skills.**

This is the same inverted logic used in job-hunt-accelerator. We ask: "Does this candidate have what the job demands?" not "Does this job match the candidate's skills?"

---

## Scoring Methodology

### Step 1: Extract Required Skills from Job Description

Parse the job posting from the career page and identify **required skills** (not nice-to-haves). These typically appear in:
- "Required Skills" or "Must Have" sections
- Language like "required," "must have," "essential"
- Technical stack explicitly mentioned as prerequisites

**Examples:**
- Career page job: "Senior Backend Engineer" → Required: [Java, Spring Boot, Microservices, Docker, AWS]
- Career page job: "Frontend Engineer" → Required: [React, TypeScript, CSS, REST APIs, Git]

Count: Total number of distinct required skills = `required_skill_count`

### Step 2: Extract Resume Skills

Parse the resume and identify all skills the candidate possesses:
- Programming languages
- Frameworks, libraries, tools
- Cloud platforms, infrastructure tools
- Soft skills (if relevant to job)
- Years of experience in each area

Store as a normalized set to handle variations (e.g., "AWS" = "Amazon Web Services").

### Step 3: Calculate Match Percentage

```
matched_required_skills = count of job's required skills that appear in resume
match_percentage = (matched_required_skills / required_skill_count) * 100
```

**Example:**
- Job requires: [Java, Spring Boot, Microservices, Docker, SQL, AWS] (6 skills)
- Resume has: [Java ✓, Spring Boot ✓, Docker ✓, SQL ✓, AWS ✓, Kubernetes, Python]
- Match: 5 / 6 = **83%** ✅ Pass (≥80%)

### Step 4: Create Match Summary

For jobs that pass the 80% threshold, prepare:

```
- Matched skills: List the 3–5 most critical required skills found in resume
- Gap skills: List the 1–2 most critical skills missing from resume (if any)
- Context: 1–2 sentence narrative (e.g., "Strong Java/Spring Backend foundation. Missing microservices depth, but Docker experience is adjacent.")
```

---

## Secondary Scoring Factors (Tie-Breaking Only)

Use these to refine ranking when multiple jobs hit 80%+:

| Factor | Adjustment | Notes |
|--------|------------|-------|
| Seniority alignment | ±5 points | Job level matches resume level |
| Geography fit | +10 points | Location matches client preference |
| Domain relevance | +5 points | Domain expertise (e.g., payments, infra) aligns |

**Example:**
- Base match: 83% (required skills)
- Seniority match: +5 (both "Senior")
- Location match: +10 (both San Francisco)
- **Final score: 98%** (for ranking, but threshold is still 80% minimum)

---

## Handling Edge Cases

### Vague Job Descriptions (Career Pages)
If the career page posting is sparse or doesn't clearly list required skills:
1. Infer required skills from job title + context (e.g., "Senior Backend Engineer" → Java, Spring, Microservices, AWS, CI/CD)
2. Use industry standard skill sets for that role
3. Flag to client: "Job posting was sparse; estimated required skills based on role title"

### Resume Gaps
- **No salary data on career page:** Leave Column J blank (don't estimate or assume)
- **Resume is very sparse:** Lean on what's explicitly stated; don't infer skills not mentioned
- **Candidate has adjacent skills:** Count as a partial match if the skill is fundamentally transferable (e.g., Terraform → Ansible, Python → Go)

### Skill Variations
Normalize common variations:
- "AWS" = "Amazon Web Services" = "EC2" (if EC2 is only AWS experience listed)
- "K8s" = "Kubernetes"
- "CI/CD" = "Jenkins" + "GitLab CI" (if both are mentioned)
- "Microservices" = "distributed systems" (similar concept)

---

## Threshold Rules

| Match % | Action |
|---------|--------|
| ≥80% | **PASS** — Surface to client for review |
| 70–79% | **REVIEW** — Show to client with "near match" flag; let them decide |
| <70% | **SKIP** — Don't show (candidate missing too many core skills) |

**Current requirement:** Only surface ≥80% matches.

---

## Scoring Script Pseudocode

```python
def score_job_match(resume_text, job_description):
    """
    Score a single job against a resume.
    Returns: match_percentage, matched_skills, gap_skills, summary
    """
    
    # Extract required skills from job description
    required_skills = extract_required_skills(job_description)
    
    # Extract skills from resume
    resume_skills = extract_resume_skills(resume_text)
    
    # Find intersection
    matched = set(required_skills) & set(resume_skills)
    gap_skills = set(required_skills) - set(resume_skills)
    
    # Calculate percentage
    match_pct = (len(matched) / len(required_skills)) * 100
    
    # Build summary
    summary = f"Match: {match_pct:.0f}% required skills. " \
              f"Strong: {', '.join(list(matched)[:3])}. " \
              f"Gap: {', '.join(list(gap_skills)[:2]) if gap_skills else 'None'}"
    
    return {
        'match_percentage': match_pct,
        'matched_skills': list(matched),
        'gap_skills': list(gap_skills),
        'summary': summary,
        'passes_threshold': match_pct >= 80
    }
```

---

## Implementation Notes for Skill Execution

1. **Call this scoring function for every job** extracted from career pages
2. **Only append to gSheet jobs with `passes_threshold == True`**
3. **Store match_percentage** for gSheet column (typically Column E)
4. **Store matched_skills and gap_skills** for the client review display (Step 6)
5. **Do NOT broaden searches** — if a career page has few matching jobs, that's the result; don't reduce filtering

---

## Example Scoring Session (Career Pages)

**Resume:** Jameson (Senior Backend Engineer, Java, Spring Boot, AWS, Docker, 8 yrs experience)

**Job 1:** Senior Java Developer @ Plaid (from career page)
- Required: [Java, Spring Boot, Microservices, AWS, Docker, SQL]
- Resume has: [Java ✓, Spring Boot ✓, Docker ✓, AWS ✓, SQL ✓] + Microservices (inferred from 8 yrs senior role)
- **Match: 6/6 = 100%** ✅ PASS

**Job 2:** DevOps Engineer @ Google (from career page)
- Required: [Kubernetes, Terraform, AWS, CI/CD, Linux]
- Resume has: [AWS ✓, Docker (similar to K8s)] + Linux (likely, 8 yrs backend)
- **Match: 2/5 = 40%** ❌ SKIP (missing K8s, Terraform, CI/CD depth)

**Job 3:** Platform Engineer @ Uber (from career page)
- Required: [Kubernetes, Go, gRPC, Observability, AWS]
- Resume has: [AWS ✓] + Java/Spring (not Go), Docker (adjacent to K8s)
- **Match: 1.5/5 ≈ 30%** ❌ SKIP

---

## Why This Works

By inverting the logic to "Does the resume cover what the job needs?" you:
1. **Reduce false positives** — Not every job that mentions "Java" is a fit
2. **Respect job requirements** — The job owner defines what's essential, not the candidate
3. **Speed up applications** — Client only pursues roles where they have 80%+ of required skills
4. **Build confidence** — "I have most of what they need" vs. "I hope they want what I know"

This logic applies identically whether you're searching job boards (Indeed/Dice) or company career pages.
