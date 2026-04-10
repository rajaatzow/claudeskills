# Application Package Generation Guide

## Overview

For each approved job (≥80% resume match), generate a tailored application package:
1. **Customized Cover Letter** — Role-specific narrative
2. **Rewritten Resume Bullets** — Highlight relevant experience
3. **PDF Resume** — Professional formatting with tailored bullets

This increases callback rates by showing alignment with the job's specific requirements.

---

## Cover Letter Structure

### Formula (Use for Every Job)

**Paragraph 1: Hook + Context**
- "I'm excited to apply for [Job Title] at [Company]"
- 1–2 sentences on why you're interested in the role / company
- *Tip: Reference something specific about the company or role from the job posting*

**Paragraph 2: Skill Alignment**
- "My experience aligns directly with your needs:"
- List 3 required skills from the job + your experience with each (1 sentence per skill)
- *Use the matched_skills from Step 5 scoring*

**Paragraph 3: Relevant Story**
- 1 short anecdote (3–4 sentences) showing you doing something the job requires
- "At [Previous Company], I [accomplished X] using [Skill Y], which led to [result]"
- *Pick a story where your skill directly solved a problem the new job will ask you to solve*

**Paragraph 4: Gap Acknowledgment (Optional)**
- If you're missing 1–2 required skills, briefly acknowledge and explain why you're still a fit
- "While my [Skill Gap] experience is limited, my [Adjacent Skill] background and [Track Record] position me to ramp quickly"
- *Don't over-explain; confidence matters*

**Paragraph 5: Close**
- "I'd welcome a conversation about how I can contribute to [Company/Team]"
- "Thank you for your consideration. I'm excited about this opportunity."
- *Keep it brief and professional*

### Example Cover Letter

```
Dear [Hiring Manager],

I'm excited to apply for the Senior Backend Engineer position at [Company]. 
Your focus on building scalable distributed systems aligns perfectly with my 
experience leading infrastructure-level initiatives at [Previous Company].

My background directly matches your core requirements:

• Java & Spring Boot: 8 years of production experience, including leading a team 
  of 5 engineers on a microservices rewrite that reduced latency by 40%

• AWS & Infrastructure: Designed and deployed containerized applications on EC2, 
  ECS, and Lambda, managing infrastructure-as-code with Terraform

• Microservices Architecture: Led the decomposition of a monolithic system into 
  20+ independent services, establishing async communication patterns with Kafka

At [Previous Company], I architected a real-time event processing pipeline that 
handled 500K+ events/second using Java, Spring Boot, and AWS. This experience 
directly mirrors the distributed systems work your team does.

While my Kubernetes experience is limited (I've worked with ECS), my Docker and 
containerization expertise, combined with my track record shipping at scale, 
positions me to ramp quickly on K8s-specific patterns.

I'd welcome a conversation about how I can contribute to [Company]'s backend 
infrastructure. Thank you for considering my application.

Best regards,
[Your Name]
```

---

## Resume Bullet Rewriting Rules

### Original Bullets (Generic)
```
• Led backend infrastructure project
• Worked with Java and AWS
• Improved system performance
```

### Rewritten Bullets (Role-Aligned)
```
• Architected microservices migration from monolith to 20+ independent Java/Spring 
  Boot services deployed on AWS ECS, reducing system latency by 40% and enabling 
  independent scaling
  
• Designed and implemented async event processing pipeline using Kafka and Spring 
  Cloud Stream, handling 500K+ events/second with 99.9% durability

• Led infrastructure-as-code initiative using Terraform and CloudFormation, 
  reducing deployment time from 2 hours to 15 minutes and enabling 50+ deploys/day
```

### Rewriting Principles

1. **Match the job's language** — Use terminology from the job posting (e.g., if job says "microservices," say "microservices"; don't say "distributed systems")
2. **Lead with impact** — Start with what you did and achieved, not the tool
3. **Include metrics** — "40% faster," "500K events/second," "50+ deploys/day" — concrete numbers land
4. **Connect to job requirements** — Each rewritten bullet should map to a required skill from the job posting
5. **Be specific, not generic** — "Improved system performance" → "Reduced P99 latency from 800ms to 120ms"

### Rewrite Process

For each bullet on your resume:
1. **Identify the core achievement** — What did you actually do?
2. **Check job posting** — Does this achievement match any required skills?
3. **If yes** — Rewrite using job posting language + metrics
4. **If no** — Consider removing or deprioritizing this bullet for this job

### Example Rewrites

**Original:** "Contributed to DevOps team"
**Rewritten (for DevOps role):** "Reduced infrastructure provisioning time from 4 hours to 30 minutes using Terraform and automated CI/CD pipelines, enabling on-demand environment scaling"

**Original:** "Used Python for backend work"
**Rewritten (for Python + Microservices role):** "Built 3 independent Python microservices using FastAPI and asyncio, deployed to Kubernetes with auto-scaling based on CPU/memory metrics, handling 10K+ concurrent users"

---

## PDF Resume Generation

### Template Structure

Use a clean, ATS-friendly template:

```
[Your Name]
[Your Email] | [Your Phone] | [Your LinkedIn] | [Your GitHub]

PROFESSIONAL SUMMARY (2–3 lines)
[Tailored summary matching job requirements]

EXPERIENCE
[Company], [Job Title] ([Dates])
• [Rewritten Bullet 1 — top achievement aligned with job]
• [Rewritten Bullet 2 — second achievement]
• [Rewritten Bullet 3 — third achievement]

[Previous Company], [Previous Title] ([Dates])
[Include only 2–3 most relevant bullets for this job]

TECHNICAL SKILLS
[Organize by category: Languages, Frameworks, Databases, Cloud, Tools]
[Prioritize skills from the job posting]

EDUCATION
[Degree, School, Year]

CERTIFICATIONS
[If applicable and relevant to job]
```

### Tools for PDF Generation

**Option A: Python + reportlab**
```python
from reportlab.lib.pagesizes import letter
from reportlab.lib.styles import getSampleStyleSheet
from reportlab.platypus import SimpleDocTemplate, Paragraph, Spacer

def generate_resume_pdf(filename, name, summary, experience, skills):
    doc = SimpleDocTemplate(filename, pagesize=letter)
    story = []
    
    # Add name
    story.append(Paragraph(name, getSampleStyleSheet()['Heading1']))
    story.append(Spacer(1, 12))
    
    # Add summary, experience, skills
    # ... (build story with sections)
    
    doc.build(story)
```

**Option B: Google Docs → PDF Export**
1. Create resume in Google Docs with tailored bullets
2. Export as PDF
3. Rename: `[Name]_[Company]_[Role]_Resume.pdf`

**Option C: Markdown → HTML → PDF**
```bash
# Create resume.md with tailored content
# Convert to PDF using pandoc
pandoc resume.md -o resume.pdf --pdf-engine=wkhtmltopdf
```

### Naming Convention

```
[FirstName]_[LastName]_[Company]_[Role]_Resume.pdf

Examples:
Jameson_Kumar_JPMorgan_SeniorBackendEngineer_Resume.pdf
Jameson_Kumar_Stripe_LeadSoftwareEngineer_Resume.pdf
```

---

## Cover Letter + Resume Delivery

### Single PDF (Embedded)
1. Generate resume PDF
2. Add cover letter as page 2
3. Result: `[Name]_[Company]_CoverLetter+Resume.pdf`
4. Client uploads single file to job application portal

### Separate PDFs
1. Generate resume PDF: `[Name]_[Company]_Resume.pdf`
2. Generate cover letter PDF: `[Name]_[Company]_CoverLetter.pdf`
3. Client uploads both as separate files (if needed)

### Ask Client Preference Once
At the start of the first job application, ask:
> "When I generate your customized application packages, would you prefer:
> A) Single PDF with cover letter + resume combined, or
> B) Two separate PDFs so you can upload them independently?"

Use their choice for all subsequent jobs in that session.

---

## Quality Checklist Before Delivery

- [ ] Cover letter addresses the specific role and company
- [ ] Skill alignment section includes 3+ required skills from job posting
- [ ] Story/example is concrete and shows the skill being used
- [ ] Resume bullets are rewritten to match job posting language
- [ ] All metrics are accurate and not inflated
- [ ] PDF is formatted cleanly (no text cutoff, readable fonts)
- [ ] File naming follows convention: `[Name]_[Company]_[Role]_Resume.pdf`
- [ ] No typos in names, company names, or job titles

---

## Example Delivery

After generating all application packages, present to client:

```
✅ Application Packages Ready

Generated 4 customized packages for your approval:

1. Jameson_JPMorgan_SeniorBackendEngineer_Resume.pdf
   Company: JPMorgan Chase
   Role: Senior Backend Engineer - Java/Spring
   Location: Jersey City, NJ
   Match: 100%

2. Jameson_Stripe_LeadSoftwareEngineer_Resume.pdf
   Company: Stripe
   Role: Lead Software Engineer
   Location: Remote
   Match: 92%

[... more ...]

All files are ready to download and submit.
```

---

## Why Customization Matters

Generic resumes don't work because:
- ATS systems scan for job-posting keywords; custom bullets improve keyword match
- Hiring managers spend 6 seconds scanning; custom bullets front-load relevance
- Cover letters show intent; generic letters suggest mass-applying

Tailored packages demonstrate:
- You understand the role
- You have the specific skills they need
- You're serious about this position (not spray-and-pray)

This increases callback rates by 3–5x compared to generic applications.
