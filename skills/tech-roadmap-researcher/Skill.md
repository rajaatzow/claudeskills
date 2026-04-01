---
name: tech-roadmap-researcher
description: Analyze a software engineer's resume or LinkedIn profile to identify high-ROI employee career moves at companies. Use this skill whenever a user uploads a resume/LinkedIn profile and wants to understand next career steps, skill gaps, and learning paths for employee roles (promotions, lateral moves, raises at companies). Generates a detailed roadmap with 1-3 possible next job titles, gap analysis, and specific learning resources with time estimates. Focus strictly on employee career paths—no self-employment, entrepreneurship, or side projects.
---

# Tech Roadmap Researcher

A skill for analyzing software engineers' current skills and positioning them for high-ROI career moves with clear, actionable learning paths.

## What This Skill Does

Given a resume or LinkedIn profile, this skill analyzes **employee career paths at companies** (no self-employment, no side projects):

1. **Extracts Current State:** Role, seniority level, tech stack, industry, years of experience, compensation signals
2. **Identifies Next Moves:** 1-3 realistic employee career paths at companies:
   - **Option A:** Same job title, higher level/company → 30-50% raise (Senior FE → Senior FE at FAANG)
   - **Option B:** Lateral move to adjacent IC role (Backend → Full-Stack, FE → Platform Engineer)
   - **Option C:** Vertical promotion (Senior IC → Staff Engineer, IC → Engineering Manager)
3. **Gap Analysis:** For each path, identifies specific technical or leadership skills needed
4. **Learning Roadmap:** 
   - Specific resources (courses, books, real projects) in learning order
   - Time estimates based on current skill level and target role difficulty
   - Prerequisite knowledge flagged
5. **Outputs:** Downloadable `.md` report with all of the above, optimized for job search and interview prep

## How to Use This Skill

**Input options:**
- Upload a resume (PDF or DOCX)
- Paste LinkedIn profile URL (if public or you have access)
- Paste resume text directly

**Expected output:**
- A `.md` file with career analysis, skill gaps, and learning resources
- Named: `roadmap-{firstname-lastname}-{date}.md`
- Saved to `/mnt/user-data/outputs/`

## The Process

### 1. Parse the Profile

Extract these key pieces:
- **Current Role:** Job title, level (junior/mid/senior/principal)
- **Tech Stack:** Languages, frameworks, tools, specializations
- **Industry Context:** FAANG, startup, enterprise, specific domain
- **Years of Experience:** Total and in current role
- **Key Strengths:** What they're genuinely good at (inferred from roles/projects)

### 2. Research Career Paths

For each possible next move, determine:
- **Market Demand:** How many roles exist for this title and level?
- **Compensation Range:** What salary/equity can they expect?
- **Hiring Criteria:** What skills/experience do companies want?
- **Time to Ready:** How long to close the skill gap?

**Research sources:**
- roadmap.sh (for tech skill progressions by role)
- levels.fyi (compensation by role/level/company)
- Job postings on LinkedIn/Indeed/Redfin (actual hiring patterns)
- Your knowledge of FAANG hiring, startup career tracks
- Blind, Glassdoor (real salary/level data)

### 3. Build Gap Analysis

For each career path, list:
- **Critical Gaps:** Skills they absolutely need (blocks them from applying)
- **Nice-to-Have Gaps:** Skills that make them more competitive
- **Depth Gaps:** Skills they know but need to deepen

Example:
```
Path: Senior Frontend Engineer
├── Critical: Advanced React patterns (hooks, state management, performance)
├── Critical: System design for frontend (scaling, caching, monitoring)
├── Nice-to-Have: TypeScript mastery
└── Depth: CSS (especially modern layout, responsive design)
```

### 4. Create Learning Path

For each gap, provide:

1. **Skill Name & Why It Matters**
   - What problem does it solve?
   - How does it position them?

2. **Learning Resources** (in order):
   - Primary resource (course, book, project)
   - Supporting resources (practice, documentation, community)
   - Cost (free/paid)

3. **Time Estimate** (based on current skill level):
   - Beginner-friendly: "20-30 hrs if familiar with X"
   - Intermediate: "10-15 hrs to deepen"
   - Advanced: "5-8 hrs to master"

4. **Proof of Mastery**:
   - What should they build or demonstrate?
   - How to add it to their resume/portfolio?

Example:
```
### Skill: Advanced React Patterns
Why: Most senior roles require deep React knowledge for architecture & code review.

Learning Path (8-10 weeks, 10-15 hrs/week):
1. **Week 1-2: Hooks Deep Dive**
   - Epic React: "Advanced Hooks" by Kent C. Dodds (paid, 2 hrs)
   - React Hooks docs: https://react.dev/reference/react
   - Practice: Convert 3 class components to hooks
   - Time: 6-8 hrs

2. **Week 3-4: State Management**
   - Redux or Zustand (based on current stack)
   - Read: "Redux Fundamentals" docs
   - Practice: Build a mini app with Redux (8-10 hrs)
   - Time: 10-12 hrs

3. **Week 5-6: Performance Optimization**
   - Course: "Optimize React" on Frontend Masters (2 hrs)
   - Read: React performance guide
   - Practice: Profile & optimize existing app (6-8 hrs)
   - Time: 8-10 hrs

Proof: Build a high-performance dashboard with complex state management.
Portfolio: Open-source contribution or GitHub project.
```

## Output Format

Generate a `.md` file with this structure:

```markdown
# Career Roadmap: [Name] — [Current Role]

**Analysis Date:** [Date]

---

## Your Current Profile

**Last Role:** [Title] @ [Company]
**Tech Stack:** [Key technologies]
**Experience:** [X years], [progression]

---

## 3 High-ROI Next Moves (At Companies)

### Move #1: [Job Title] at [Company Type]

**Compensation:** [Salary range]  
**Fit:** [%]  
**Timeline:** [Duration]

#### Skill Gaps
- **Critical:** [Must-have skills]
- **Nice-to-Have:** [Competitive advantages]

#### Learning Path
[Specific resources, time estimates, proof of mastery]

---

### Move #2: [Job Title]
[Same structure]

---

### Move #3: [Job Title]
[Same structure]

---

## Summary

| Move | Timeline | Comp | Risk | Best If... |
|------|----------|------|------|-----------|
| [Move #1] | [Time] | [Comp] | [Risk] | [Criteria] |
| [Move #2] | [Time] | [Comp] | [Risk] | [Criteria] |
| [Move #3] | [Time] | [Comp] | [Risk] | [Criteria] |
```

## Key Principles

1. **Be Specific:** "Learn React" is useless. "Master React hooks, context API, and performance optimization" is actionable.

2. **Respect Their Time:** Give honest time estimates. Acknowledge that some skills take 2 weeks, others take 6 months. Don't sugarcoat.

3. **Provide Concrete Resources:** Link to real courses, books, documentation. Don't say "find a course"—say which one.

4. **Match Learning to Seniority:** A senior engineer learning a new framework doesn't start at "Hello World." Adjust depth and pacing.

5. **Emphasize Proof, Not Certificates:** A built project > a completed course. Design the learning around portfolio-building.

6. **Connect to Compensation:** Help them see the link between skill-building and salary growth. Knowing "Senior FE Engineer makes $200-280k" is motivating.

## Edge Cases

**If resume is vague or incomplete:**
- Ask clarifying questions (in the report or in chat)
- Make reasonable inferences based on job titles
- Flag assumptions: "I'm assuming 3 years of React experience based on your roles—correct?"

**If they're already highly skilled:**
- Focus on breadth (adjacent domains) or depth (mastery in one area)
- Consider leadership moves (Tech Lead, Architect, Manager)

**If they're switching industries:**
- Flag the risk/timeline upfront
- Suggest "bridge" roles that leverage existing skills
- Be honest about compensation reset (usually 10-30%)

## Tools & References

- **Resume parsing:** Extract text from PDF/DOCX, infer structure
- **Roadmap research:** roadmap.sh, your knowledge of tech progressions
- **Compensation research:** levels.fyi, Blind, Glassdoor
- **Market research:** LinkedIn (role prevalence), job boards (JD trends)

---

## Claude.ai Implementation Notes

When running this skill in Claude.ai:

1. If given a resume file, use the `web_fetch` or `view` tool to read it
2. If given a LinkedIn URL, navigate to it (or ask the user to paste their profile text)
3. Research career paths by:
   - Using web_search for compensation data, market trends
   - Leveraging your knowledge of roadmap.sh and tech career paths
   - Cross-referencing with your training on FAANG hiring, startup ecosystems
4. Generate the .md file directly to `/mnt/user-data/outputs/`
5. Keep learning resources specific: include URLs, course names, estimated hours

---

Done. Ready to test with a resume or LinkedIn profile.
