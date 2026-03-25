---
name: resume-optimizer-dev
description: Optimize software engineer and developer resumes for ATS systems, job descriptions, and impact. Use this skill whenever a user uploads a resume (.docx or PDF) and wants to improve it—whether they're tailoring it to a specific job posting, optimizing for ATS scanners, or strengthening technical language and metrics. Trigger on phrases like "optimize my resume", "tailor my resume to this job", "improve my resume", "help me with my resume", "make my resume better for this position", or when a user shares a resume file alongside a job description or job URL.
---

# Resume Optimizer for Software Engineers & Developers

This skill helps developers and software engineers strengthen their resumes by providing actionable, tracked feedback that balances multiple optimization goals.

## What This Skill Does

When a user provides:
1. A resume file (PDF or .docx), optionally with
2. A job description or job URL to tailor against

The skill analyzes the resume and delivers **annotated feedback with tracked changes** across five technical dimensions:

- **ATS Optimization**: Keyword alignment, formatting for machine parsing, standard section headers
- **Job Description Matching**: Technical skills and keywords from the target job description
- **Technical Depth & Specificity**: Adding concrete details, tools, frameworks, and methodologies (instead of vague language)
- **Quantifiable Impact**: Adding metrics, performance improvements, and measurable outcomes
- **Technical Skills Organization**: Prominence and organization of technical competencies in skills section

## Workflow

### Step 1: Prepare the Resume for Analysis
1. If the user uploaded a PDF or .docx, extract the text content
2. If they provided a job URL, fetch and parse the job description
3. If they pasted a job description, use it directly
4. Note any specific goals the user mentioned (e.g., "I want to land this job at Company X")

### Step 2: Analyze the Resume
Evaluate the resume against all five technical dimensions. Look for:
- Missing or weak technical keywords (especially if matched against a JD)
- Vague accomplishments ("Worked on projects") vs. specific technical work ("Implemented distributed caching layer using Redis")
- Unexplained technologies or tools (just listing names vs. showing context)
- Missing metrics or performance outcomes (speed, scale, reliability improvements)
- Technical skills that aren't organized for ATS scanning (alphabetized, no frameworks/tools listed)
- Opportunity to add specific technologies from the target job description

### Step 3: Generate Tracked Changes
Use a **clear, structured format** showing suggested edits. The goal is to present feedback that the user can accept, reject, or refine:

**Format for suggestions:**
```
[SECTION: Experience]
[ORIGINAL]: "Worked on backend systems for e-commerce platform"
[SUGGESTED]: "Built and optimized backend microservices (Python/FastAPI) handling 500+ transactions/sec, reducing API latency by 35% through query optimization and Redis caching"
[REASON]: Adds specific tech stack, quantifiable scale, and performance metrics. Better ATS match for positions emphasizing scalability.

[SECTION: Skills]
[ORIGINAL]: "Languages: Python, JavaScript, Java"
[SUGGESTED]: "Languages: Python (5 years, async/await, FastAPI), JavaScript (4 years, async patterns), Java (3 years, Spring Boot) | Frameworks: FastAPI, React, Spring Boot | Databases: PostgreSQL (optimization, transactions), Redis (caching), MongoDB"
[REASON]: Adds depth and frameworks. Includes database expertise. Reorder by strongest/most recent skill first.
```

Repeat for each significant opportunity. Aim for 5–15 suggestions depending on resume length and quality.

### Step 4: Prioritize Suggestions
If the user provided a job description:
- **High priority**: Keywords, technologies, and skills from the JD that are missing or underemphasized
- **Medium priority**: Adding specificity (tech stack, frameworks), quantifying technical outcomes
- **Low priority**: ATS formatting tweaks, skill section reorganization

If no job description:
- **High priority**: Adding specificity and quantifiable technical outcomes
- **Medium priority**: Expanding technical depth (frameworks, databases, tools)
- **Low priority**: ATS formatting, skill section organization

### Step 5: Present Results
Deliver suggestions in a **numbered list** so the user can easily reference and decide on each one:

> Here are my suggested changes (organized by priority):
>
> **High Priority (Job-Specific Matches)**
> 1. [SECTION: Experience] ... [REASON]: ...
> 2. [SECTION: Skills] ... [REASON]: ...
> 
> **Medium Priority (Impact & Clarity)**
> 3. ...
> 
> **Low Priority (Formatting & Polish)**
> 4. ...

## What NOT to Do

- **Don't suggest generic soft skills** (communication, collaboration, leadership, quick learner, etc.)—these don't help modern resumes
- **Don't claim to know the hiring manager's exact preferences**—say "likely to resonate" instead of "will definitely"
- **Don't remove legitimate experience or lie**—only add specificity and reframe what's already there
- **Don't suggest cutting years of experience**—instead, help reorganize and strengthen what's present
- **Don't over-format suggestions**—keep them clear and scannable
- **Don't ignore the user's stated goals**—if they say "I want to move into DevOps," prioritize DevOps keywords and technologies

## Edge Cases

**Resume is already strong**: Offer subtle improvements (verb swaps, metrics refinement, ATS tweaks) and let the user know it's in good shape.

**Resume has gaps or weak experience**: Focus on reframing what IS there, not critiquing what's missing. Suggest how to highlight relevant parts of past roles.

**Job description is very different from resume background**: Be honest: "This is a significant pivot. Consider highlighting any adjacent skills (e.g., infrastructure experience for a DevOps role even if you're currently backend)."

**User provides only a resume, no JD**: Provide general ATS optimization, clarity improvements, and impact strengthening—without job-specific suggestions.

## Example Interaction

**User**: "I'm applying to a senior backend engineer role at Stripe. Here's my resume [uploads .docx] and here's the job posting [pastes/links]"

**Skill**: Extracts resume, parses job description, identifies gaps and opportunities, returns 8–12 prioritized suggestions organized by impact.

**User**: Reviews suggestions, accepts most, rejects/refines a few, applies changes to their resume.

---

## Limitations

- This skill provides suggestions, not guarantees of interviews or hires
- Some industries have niche ATS systems with unique requirements (this covers general best practices)
- The skill cannot evaluate soft skills or cultural fit—only resume content and presentation
- If a resume is fundamentally misaligned with the target role, reframing has limits

---

## Success Criteria

A successful interaction:
- User understands each technical suggestion and why it adds value for the target role
- Suggestions are actionable (add specific tools, frameworks, metrics, or technical details)
- User feels their resume better represents their technical depth and experience
- User can apply changes confidently (whether manually or with Claude's help)
