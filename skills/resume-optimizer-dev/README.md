# Resume Optimizer for Software Engineers & Developers

A Claude skill that helps software engineers and developers strengthen their resumes through technical feedback and optimization recommendations.

## Overview

**Resume Optimizer for Developers** analyzes your resume and provides **5-15 prioritized technical suggestions** focused on:
- **ATS Optimization** - Keyword alignment and machine parsing
- **Job Description Matching** - Technical skills alignment with target roles
- **Technical Depth & Specificity** - Adding concrete tools, frameworks, and methodologies
- **Quantifiable Impact** - Metrics, performance improvements, and measurable outcomes
- **Technical Skills Organization** - Clear, scannable skill section structure

## Key Features

✅ **Focused on technical content** - No soft skill padding (no "great communicator" or "team player")
✅ **Prioritized feedback** - High/Medium/Low priority so you know what to tackle first
✅ **Job-specific alignment** - Matches technologies from job descriptions when provided
✅ **Actionable suggestions** - Each suggestion includes original text, suggested revision, and reasoning
✅ **Works with all experience levels** - Bootcamp grads to senior/principal engineers
✅ **Multiple specializations** - Frontend, Backend, DevOps, Full-stack, and infrastructure roles
✅ **Tracked changes format** - Easy to accept, reject, or refine suggestions

## How It Works

### Input
Provide:
1. **Your resume** (PDF or .docx file)
2. **Optional: Target job description** (URL, pasted text, or job description file)
3. **Optional: Specific goal** (e.g., "I want to target payment systems roles")

### Output
You receive:
- 5-15 prioritized suggestions organized by impact level
- Each suggestion shows:
  - Original text from your resume
  - Suggested revision
  - Reasoning for the change
- Organized in a clear, numbered list format

### Example Suggestion

```
[SECTION: Experience]
[ORIGINAL]: "Worked on backend systems for e-commerce platform"
[SUGGESTED]: "Built and optimized backend microservices (Python/FastAPI) handling 500+ transactions/sec, reducing API latency by 35% through query optimization and Redis caching"
[REASON]: Adds specific tech stack, quantifiable scale, and performance metrics. Better ATS match for positions emphasizing scalability.
```

## When to Use This Skill

✅ **Trigger this skill when:**
- "Optimize my resume"
- "Tailor my resume to this job"
- "Improve my resume for a backend engineering role"
- "Help me with my resume" + job description
- You upload a resume file + job posting

❌ **Skip this skill when:**
- You just need to read/understand a resume (Claude can do that directly)
- You're looking for career coaching or salary negotiation advice
- You want feedback on non-technical content

## What Gets Optimized

### ✅ Improved
- Adding specific technologies (React, FastAPI, PostgreSQL, Kafka, etc.)
- Quantifying outcomes (latency reduction, throughput improvements, uptime percentages)
- Specifying frameworks and tools (not just "APIs" but "REST APIs with OpenAPI specs")
- Organizing skills section for ATS scanning
- Adding performance metrics and scale details
- Highlighting technical depth (years per tool, specific patterns/methodologies)

### ❌ Not Addressed
- Soft skills (communication, leadership, mentorship)—these are assumed at your experience level
- Career advice (should I take this job?)
- Salary negotiation
- Cover letters or interview prep
- Formatting/design (focus is technical content)

## Example: Before & After

### Before
```
EXPERIENCE

Senior Backend Engineer | PaymentCorp | Remote | 2022 – Present
• Worked on microservices architecture using Spring Boot
• Optimized database performance
• Led team of 5 developers
• Participated in code reviews
```

### After (with suggested improvements)
```
Senior Backend Engineer | PaymentCorp | Remote | 2022 – Present
• Architected and deployed 12+ microservices (Spring Boot, Java 17) handling 10,000+ TPS with <50ms p99 latency
• Optimized PostgreSQL queries and Redis caching layer, reducing database load by 45% and improving query performance by 200ms
• Led team of 5 backend engineers; established code review standards and mentored 2 engineers promoted to mid-level
• Owned on-call rotation for critical payment systems; reduced MTTR (mean time to recovery) by 60%
```

**Why this is better:**
- Specific tech stack (Spring Boot, Java 17)
- Quantified scale (10,000+ TPS)
- Performance metrics (50ms latency, 45% load reduction)
- Actual impact (200ms improvement, 60% MTTR reduction)
- Clear leadership with outcomes (2 promoted)

## Tested Scenarios

The skill has been validated on:

- ✅ **Early-career (bootcamp grads)** - Junior Frontend Engineer → startup role
- ✅ **Mid-career (3-7 years)** - Backend Engineer → Senior Backend role at payment company
- ✅ **Mid-career (3-7 years)** - Full-stack Engineer → fintech startup
- ✅ **Senior (7+ years)** - DevOps Engineer → Staff Infrastructure role
- ✅ **Principal (20+ years)** - Principal Frontend Developer → architecture role

**Specializations covered:**
- Frontend (React, Angular, Vue)
- Backend (Python, Node.js, Java)
- DevOps/Infrastructure (Kubernetes, AWS, Terraform)
- Full-stack development

## Installation & Usage

### For Claude.ai Users

1. **Save the skill**
   - Download or copy the `SKILL.md` file
   - Upload it to your Claude workspace (when the feature is available)

2. **Use the skill**
   - Upload your resume (PDF or .docx)
   - Optionally, provide a job description
   - Ask Claude to "optimize my resume" or "tailor my resume to this job"

### For Claude API / Claude Code Users

1. **Clone or download this repository**
   ```bash
   git clone https://github.com/yourusername/resume-optimizer-dev.git
   ```

2. **Place the skill in your Claude installation**
   - Refer to Claude Code documentation for skill installation

3. **Use the skill in your workflow**
   ```
   // Ask Claude to use the skill
   "Help me optimize my resume for a senior backend engineering role at [Company]"
   ```

## Files in This Repository

```
resume-optimizer-dev/
├── SKILL.md                    # The main skill definition (required)
├── README.md                   # This file (documentation)
├── CHANGELOG.md                # Version history and improvements
├── examples/                   # Example outputs from the skill
│   ├── example-1-backend.md
│   ├── example-2-frontend.md
│   └── example-3-devops.md
└── LICENSE                     # MIT License
```

## How The Skill Analyzes Resumes

The skill evaluates resumes across **5 technical dimensions**:

### 1. ATS Optimization
- Keyword density and placement
- Standard section headers (Experience, Skills, etc.)
- Formatting for machine parsing
- Removal of symbols/graphics that break ATS systems

### 2. Job Description Matching
- Identifies technologies and skills from target job posting
- Flags missing keywords from JD
- Suggests reframing existing skills to match JD terminology

### 3. Technical Depth & Specificity
- Replaces vague language ("Worked on systems") with specific details
- Adds frameworks, databases, tools, methodologies
- Shows context for technologies (not just listing names)

### 4. Quantifiable Impact
- Identifies missing metrics and performance improvements
- Suggests adding: latency/throughput numbers, uptime percentages, cost savings
- Highlights scale (users served, data processed, team scope)

### 5. Technical Skills Organization
- Reorganizes skills by relevance and recency
- Separates modern stack from historical/legacy tech
- Adds depth (years of experience, specific patterns per tool)
- Prioritizes current technologies for ATS scanning

## What Gets Priority

When analyzing your resume, the skill prioritizes:

### High Priority (Most Impact)
- Missing technologies from job description
- Vague accomplishments lacking metrics or specificity
- Weak representation of core skills for the target role

### Medium Priority (Good Improvements)
- Adding frameworks, databases, tools to experience bullets
- Quantifying outcomes and impact
- Expanding technical depth

### Low Priority (Nice Polish)
- ATS formatting tweaks
- Skill section reorganization
- Removing outdated technologies

## Limitations & Scope

This skill **does not**:
- Guarantee interviews or job offers
- Evaluate non-technical content
- Provide career counseling
- Suggest salary ranges or negotiation strategies
- Create or generate entire resume sections (you provide the raw material)
- Handle niche/specialized ATS systems (covers general best practices)

**This skill is best for:**
- Making strong resumes even stronger
- Identifying gaps in technical detail
- Aligning resumes with specific job postings
- Removing soft skill padding in favor of technical specificity

## Best Practices

### ✅ Do
- Provide a specific job description for best results
- Be honest about your experience (skill reframes, doesn't invent)
- Focus on suggestions that reflect real work you've done
- Use metrics and specifics you can substantiate
- Accept suggestions that strengthen your resume authentically

### ❌ Don't
- Lie or exaggerate accomplishments
- Remove years of legitimate experience
- Try to make a junior role sound like a senior role
- Use all suggestions blindly (review and refine)
- Expect the skill to completely rewrite your resume

## Version History

See [CHANGELOG.md](CHANGELOG.md) for detailed version history and improvements.

**Current Version:** 1.0.0
- ✅ Initial release with 5 technical dimensions
- ✅ Tested on 5 experience levels and 4+ specializations
- ✅ Focused on technical content (no soft skills)

## Contributing & Feedback

Have suggestions? Found issues? Want to improve the skill?

1. **Test it** - Use the skill and note what works and what doesn't
2. **Report feedback** - Issues, missing features, edge cases
3. **Suggest improvements** - Better examples, clarified instructions, new features

## License

MIT License - See LICENSE file for details

## Support

**Need help?**
- Read the skill definition: `SKILL.md`
- Check example outputs in `examples/`
- Review the CHANGELOG for recent improvements

**Have questions?**
- Check if your question is answered in the "Limitations & Scope" section
- Review the "Best Practices" section
- See example scenarios in "Tested Scenarios"

---

## Quick Start

```
1. Upload your resume (PDF or .docx)
2. Paste or link your target job description (optional but recommended)
3. Ask Claude: "Optimize my resume for this job posting"
4. Review suggestions (organized by High/Medium/Low priority)
5. Apply changes that reflect your real work
6. Repeat for different target roles as needed
```

That's it! Your resume will be stronger and better-positioned for ATS systems and hiring managers.

---

**Made with ❤️ for software engineers who want technical resumes, not resume padding.**
