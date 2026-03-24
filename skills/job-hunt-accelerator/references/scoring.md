# Job Match Scoring Rubric

Score each job against the client's resume on two dimensions.

---

## Required Skills Match (gates visibility — must be ≥90% to show)

1. Extract all skills/technologies marked as **required** in the job description
2. Check each against the resume (direct mention, synonyms, or clear equivalents count)
3. Score = (required skills found on resume) / (total required skills) × 100

**Synonym/equivalent rules:**
- "K8s" = "Kubernetes"
- "GCP" = "Google Cloud Platform"
- "CI/CD" covers Jenkins, GitHub Actions, CircleCI, GitLab CI, etc.
- "Infrastructure as Code" covers Terraform, Pulumi, CloudFormation, Ansible
- "Containerization" covers Docker, Podman
- AWS certifications imply AWS experience

**Do not** give credit for preferred/nice-to-have skills appearing in the required bucket.

---

## Overall Fit Score (shown alongside required match)

A holistic second score (0–100%) factoring in:

| Factor | Weight |
|--------|--------|
| Required skills match | 50% |
| Preferred skills match | 20% |
| Seniority alignment | 15% |
| Domain/industry match | 10% |
| Location/remote alignment | 5% |

**Seniority alignment:**
- Exact match = 100%
- One level off (e.g., mid applying to senior) = 60%
- Two levels off = 20%

**Display both scores to the client:**
> "94% required skills match | 87% overall fit"

---

## Match Summary (2 sentences max)

Sentence 1: What strongly aligns (top 3 matching required skills + any standout preferred skills)
Sentence 2: The most significant gap or risk (missing required skill, seniority stretch, location mismatch)

If there are no meaningful gaps, sentence 2 can be a positive: "Resume maps well across all core requirements."
