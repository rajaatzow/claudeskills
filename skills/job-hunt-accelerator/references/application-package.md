# Application Package Generation

For each client-approved job, generate a cover letter, rewritten resume bullets, and a tailored PDF resume.

---

## Cover Letter

### Structure
```
[Client Name]
[City, State] | [Email] | [Phone] | [LinkedIn if on resume]

[Date]

Hiring Team
[Company Name]

Dear Hiring Team,

[Opening paragraph — 2–3 sentences]
[Body paragraph — 3–4 sentences]
[Closing paragraph — 2 sentences]

Sincerely,
[Client Name]
```

### Tone & Voice
- Confident, direct, professional — not sycophantic
- Written in first person
- No "I am writing to express my interest in..." openers — start with a strong value statement
- Mirror the job description's language (use their exact terminology where natural)
- Never exceed one page

### Opening paragraph
Lead with the client's strongest relevant qualification for THIS role. Reference the company by name.
Example: "With 7 years building resilient Kubernetes infrastructure at scale, I'd bring immediate impact to [Company]'s platform engineering team."

### Body paragraph
Connect 2–3 specific resume achievements to the job's top requirements. Use numbers from the resume where available (e.g., "reduced deployment time by 40%", "managed 200-node cluster").

### Closing paragraph
One sentence expressing genuine interest in the company's specific work (pull from job description context). One sentence with a clear call to action.

---

## Resume Bullet Rewriting

### Rules
- Rewrite 3–5 bullets from the client's existing experience sections that are most relevant to this job
- Do NOT invent new experience or fabricate metrics
- Mirror the job description's language and terminology where it fits naturally
- Use strong action verbs (Architected, Scaled, Automated, Reduced, Led, Deployed, Migrated)
- Format: **Action verb + what you did + result/scale** (quantified where resume already has numbers)
- Keep bullets to 1–2 lines max

### What to target for rewriting
1. Find the job's top 3–5 required skills
2. Find the resume bullets that come closest to matching those skills
3. Rewrite those bullets to use the job's exact terminology where accurate
4. If a bullet mentions a technology by one name and the JD uses another (e.g., resume says "K8s", JD says "Kubernetes"), standardize to the JD's term

### Output format
Present rewrites as a before/after table so the client can see the changes clearly:

```
EXPERIENCE: Senior Platform Engineer @ Acme Corp

BEFORE: Managed container deployments using K8s across 3 regions
AFTER:  Architected multi-region Kubernetes deployment pipelines supporting 99.9% uptime SLA

BEFORE: Wrote Terraform modules for AWS infra
AFTER:  Developed reusable Terraform modules for AWS infrastructure, reducing provisioning time by 60%
```

---

## PDF Resume Template — ZOW Standard

This is the ZOW Experienced Engineer Resume Template. Use it exactly as specified for every client resume PDF.

### Install
```bash
pip install reportlab --break-system-packages
```

### ZOW Template Spec (extracted from ZOW_Experienced_Engineer_Resume_Template.docx)

**Page:** A4 (595 × 842 pt in reportlab), margins: 0.5" all sides (36pt)
**Font:** Arial throughout (use Helvetica in reportlab — exact substitute, ATS safe)
**Colors:**
- Name: black `#000000`, bold, 22pt
- Contact line: black `#000000`, regular, 10pt
- Target job title: dark grey `#333333`, bold, 12pt, centered
- Section headers: black `#000000`, bold, SMALL CAPS, 12pt, left-aligned, bottom border line (black, 0.5pt)
- Job title line: black `#000000`, bold, 11pt
- Company/date/location line: black `#000000`, regular, 11pt
- Body text / bullets: black `#000000`, regular, 11pt
- Skills line: black `#000000`, regular, 11pt (pipe-separated, no categories needed)

**Section header style:** Bold, small caps, 12pt, black, with a thin black bottom border line underneath. Spacing: 12pt before, 4pt after.

**Sections in order:**
1. Name (centered, bold, 22pt)
2. Contact line (centered, 10pt): `City, State | phone | email | linkedin`
3. Target Job Title (centered, bold, 12pt, dark grey #333333)
4. Professional Summary (section header + paragraph body)
5. Technical Skills & Software Tools (section header + single pipe-separated line)
6. Professional Experience (section header + jobs in reverse chronological order)
7. Education (section header + degree entries)
8. Certifications (section header + list, if present)

### Python Generation Code

```python
from reportlab.lib.pagesizes import A4
from reportlab.lib.styles import ParagraphStyle
from reportlab.lib.units import inch, pt
from reportlab.platypus import SimpleDocTemplate, Paragraph, Spacer, HRFlowable
from reportlab.lib import colors
from reportlab.lib.enums import TA_LEFT, TA_CENTER

BLACK = colors.HexColor('#000000')
DARK_GREY = colors.HexColor('#333333')
MARGIN = 0.5 * inch

def build_resume_pdf(output_path, resume_data):
    """
    resume_data = {
        "name": str,
        "contact": str,       # "City, State | (555) 555-5555 | email@email.com | linkedin.com/in/name"
        "target_title": str,  # e.g. "Senior DevOps Engineer"
        "summary": str,       # full paragraph — 4–5 sentences per ZOW structure
        "skills": [str],      # flat list — will be joined with " | "
        "experience": [
            {
                "title": str,
                "company": str,
                "location": str,
                "dates": str,         # "MM/YYYY – Present" or "MM/YYYY – MM/YYYY"
                "bullets": [str]
            }
        ],
        "education": [
            {
                "degree": str,        # e.g. "Bachelor of Science, Computer Science"
                "school": str,
                "location": str,
                "years": str,         # e.g. "2010 – 2014"
                "coursework": str     # optional
            }
        ],
        "certifications": [str]  # optional
    }
    """
    doc = SimpleDocTemplate(
        output_path,
        pagesize=A4,
        leftMargin=MARGIN,
        rightMargin=MARGIN,
        topMargin=MARGIN,
        bottomMargin=MARGIN
    )

    def section_header(text):
        """Section header: bold, small caps effect (uppercase), 12pt, black, with bottom border."""
        return [
            Paragraph(
                f"<b>{text.upper()}</b>",
                ParagraphStyle("sh", fontName="Helvetica-Bold", fontSize=12,
                               textColor=BLACK, spaceBefore=12, spaceAfter=0,
                               alignment=TA_LEFT)
            ),
            HRFlowable(width="100%", thickness=0.5, color=BLACK, spaceAfter=4)
        ]

    story = []

    # 1. Name
    story.append(Paragraph(
        resume_data["name"],
        ParagraphStyle("name", fontName="Helvetica-Bold", fontSize=22,
                       textColor=BLACK, alignment=TA_CENTER, spaceAfter=2)
    ))

    # 2. Contact line
    story.append(Paragraph(
        resume_data["contact"],
        ParagraphStyle("contact", fontName="Helvetica", fontSize=10,
                       textColor=BLACK, alignment=TA_CENTER, spaceAfter=4)
    ))

    # 3. Target job title
    story.append(Paragraph(
        f"<b>{resume_data['target_title']}</b>",
        ParagraphStyle("title", fontName="Helvetica-Bold", fontSize=12,
                       textColor=DARK_GREY, alignment=TA_CENTER, spaceAfter=8)
    ))

    body_style = ParagraphStyle("body", fontName="Helvetica", fontSize=11,
                                textColor=BLACK, spaceAfter=3, leading=14)
    bold_style = ParagraphStyle("bold", fontName="Helvetica-Bold", fontSize=11,
                                textColor=BLACK, spaceAfter=0)
    bullet_style = ParagraphStyle("bullet", fontName="Helvetica", fontSize=11,
                                  textColor=BLACK, leftIndent=14, firstLineIndent=-10,
                                  spaceAfter=2, leading=14, bulletText="•")

    # 4. Professional Summary
    if resume_data.get("summary"):
        story.extend(section_header("Professional Summary"))
        story.append(Paragraph(resume_data["summary"], body_style))

    # 5. Technical Skills & Software Tools
    if resume_data.get("skills"):
        story.extend(section_header("Technical Skills & Software Tools"))
        skills_line = " | ".join(resume_data["skills"])
        story.append(Paragraph(skills_line, body_style))

    # 6. Professional Experience
    if resume_data.get("experience"):
        story.extend(section_header("Professional Experience"))
        for job in resume_data["experience"]:
            # Bold job title on its own line
            story.append(Paragraph(f"<b>{job['title']}</b>", bold_style))
            # Company | Location | Dates on next line
            story.append(Paragraph(
                f"{job['company']} | {job['location']} | {job['dates']}",
                ParagraphStyle("meta", fontName="Helvetica", fontSize=11,
                               textColor=BLACK, spaceAfter=3, leading=14)
            ))
            for bullet in job["bullets"]:
                story.append(Paragraph(bullet, bullet_style))
            story.append(Spacer(1, 4))

    # 7. Education
    if resume_data.get("education"):
        story.extend(section_header("Education"))
        for edu in resume_data["education"]:
            story.append(Paragraph(f"<b>{edu['degree']}</b>, {edu['years']}", bold_style))
            loc = f"{edu['school']}, {edu['location']}"
            story.append(Paragraph(loc, body_style))
            if edu.get("coursework"):
                story.append(Paragraph(f"Relevant Coursework: {edu['coursework']}", body_style))

    # 8. Certifications
    if resume_data.get("certifications"):
        story.extend(section_header("Certifications"))
        for cert in resume_data["certifications"]:
            story.append(Paragraph(cert, body_style))

    doc.build(story)
    print(f"Resume PDF saved: {output_path}")
```

### Usage
1. Parse the client's resume into the `resume_data` structure above
2. Set `target_title` to the specific job title being applied for
3. Swap in the rewritten bullets for the relevant experience entries
4. Call `build_resume_pdf(output_path, resume_data)`
5. Name the file: `[ClientLastName]_[CompanyName]_Resume.pdf`
6. Use `present_files` to deliver it

### ZOW Summary Structure
The Professional Summary must follow ZOW's 4-part structure:
1. **Capacity statement**: Role + years + domain expertise
2. **Value proposition + evidence**: Specific achievement with measurable result
3. **Standout trait**: Award, unique project, or memorable differentiator
4. **Career goal**: Why they're applying + what they bring to this specific role

### Important Rules
- Never add experience, skills, or dates that aren't on the original resume
- Skills are listed as a flat pipe-separated line (not grouped by category)
- If a section is missing from the original resume (e.g., no certifications), omit it
- Keep bullet count per job consistent with what was on the original resume
- If the resume would exceed 2 pages, trim bullets from oldest/least-relevant roles first
- Always use A4 page size (ZOW standard)
