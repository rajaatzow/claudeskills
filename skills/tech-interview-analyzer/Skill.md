---
name: technical-interview-analyzer
description: |
  Analyze technical interview recordings and transcripts to provide comprehensive, actionable feedback. Use this skill whenever a user uploads or mentions an interview recording (audio or video) or transcript and wants feedback on any aspect of interview performance. Triggers on phrases like "analyze my interview", "review this interview recording", "interview feedback", "how did I do in this interview", "analyze this interview", or when a user shares an interview file or transcript. Works with all types of technical interviews including behavioral, coding challenges, system design, technical screening, and HR screening. Special strength: Tracks improvement across multiple interview sessions and provides growth-oriented feedback with practice routines.
---
 
# Technical Interview Analyzer
 
## Overview
 
This skill analyzes technical interview recordings to provide comprehensive, actionable feedback to software engineers. It transcribes the interview, evaluates performance across multiple dimensions, and delivers structured feedback with severity ratings and improvement recommendations.
 
## Workflow
 
### Step 1: Receive and Validate Interview Recording
 
When a user provides an interview recording:
- Confirm the file format (audio or video)
- Verify file is accessible
- Identify interview type if mentioned (coding, system design, behavioral, screening, etc.)
 
### Step 2: Transcribe the Interview
 
Use Claude's audio/video transcription capabilities to convert the recording to text:
- Request permission to transcribe if needed
- Process the full interview
- Preserve timestamps and speaker identification where possible
- Note any unclear sections for later callout
 
### Step 3: Analyze Across Multiple Dimensions
 
Evaluate the interview against the following dimensions (not all apply to every interview; adapt based on content):
 
#### 1. **Problem-Solving Approach**
- Initial clarification of requirements
- Breaking down the problem into components
- Identifying constraints and edge cases early
- Approach to debugging or handling obstacles
- Overall problem-solving strategy coherence
- **Special note:** Look for ability to ask clarifying questions before jumping to solutions
 
#### 2. **Code Quality and Correctness** (coding interviews)
- Correctness of the solution
- Code organization and structure
- Handling of edge cases in implementation
- Testing mindset demonstrated
- Use of appropriate data structures and algorithms
 
#### 3. **Communication Skills & Delivery** (critical across all interviews)
- Speaking pace (not too fast, not too slow; deliberate)
- Clarity of explanations without filler words
- Active listening to interviewer feedback/hints
- Ability to articulate thought process
- Responsiveness to questions and clarifications
- Confidence and professionalism
- **Note:** Speed can undermine otherwise strong content
 
#### 4. **Specificity & Precision**
- Providing concrete metrics vs. vague descriptions
- Rating scale clarity (e.g., "8/10" vs. "very good")
- Quantified impact (latency improvements, incident reductions, etc.)
- Concrete examples with context
- **Note:** Vagueness signals uncertainty; specificity signals confidence
 
#### 5. **Self-Awareness & Growth Mindset**
- Honest acknowledgment of limitations
- Solutions implemented for identified gaps
- Genuine curiosity and willingness to learn
- Framing challenges positively
- Evidence of intentional growth/improvement
 
#### 6. **Professional Relationships & Communication**
- Respectful, balanced discussion of past managers/teammates
- No negativity or blame-shifting
- Positive framing of past experiences
- Ability to learn from others
- Team collaboration mindset
 
#### 7. **Business & Product Acumen** (if discussed)
- Understanding how technical work serves business goals
- Customer/user focus in decision-making
- Recognition of revenue impact and prioritization
- Strategic thinking beyond just execution
- **Note:** Standout strength for backend/platform engineers
 
#### 8. **Time Management** (if applicable to interview format)
- Pacing through the problem/interview
- Time allocation (planning vs execution vs validation)
- Ability to complete solution or scope appropriately
- Recovery from setbacks within time constraints
 
#### 9. **Listening & Responsiveness**
- Adjusting answers based on interviewer cues
- Following interviewer's lead vs. forcing pre-set narrative
- Engagement with follow-up questions
- Demonstrating understanding of what's being asked
 
### Step 4: Generate Structured Feedback
 
Create feedback in four distinct sections:
 
#### A. **Executive Summary**
- One-sentence overall rating (X/10 with descriptor)
- 2-3 standout strengths (honest, specific, encouraging)
- 2-3 key growth areas (prioritized by impact)
- Brief note on improvement trajectory if applicable
 
#### B. **Detailed Feedback Paragraphs**
For each relevant dimension, write 2-4 focused paragraphs:
- **What they did well:** Specific examples with timestamps (00:XX:XX format)
- **Where there's opportunity:** Frame constructively, not critically
- **Concrete example:** Direct quote or specific moment from interview
- **Actionable next step:** Specific practice technique or behavioral change
- **Severity rating:** Critical (high impact on hiring), Major (noticeable improvement needed), Minor (polish/optimization)
 
**Critical guidance:**
- Include timestamps for EVERY major observation (e.g., "00:08:42-00:08:52")
- Use direct quotes when possible (build credibility)
- Balance: Every negative point should have a strength nearby
- Be specific: "Speaking pace undermines content" not "Could be better"
- Provide implementation steps: "Record yourself at 20% slower pace" not "Speak slower"
 
#### C. **Interview Scorecard**
Create a structured table showing:
- Dimension evaluated
- Rating (1-5 scale or 1-10, be consistent)
- Severity level (if gap exists): Critical/Major/Minor/Strength
- One-sentence insight or comment
 
#### D. **Next Steps & Recommendations**
Format as prioritized sections:
1. **Critical (implement before next interview)** — 2-3 items with specific actions
2. **Major (important to address)** — 2-3 items with timelines
3. **Minor (nice to improve)** — 1-2 items
4. **Practice Routine** (if appropriate) — Daily practice script with duration and focus
5. **Success Criteria** — What improvement looks like
 
**Comparative feedback:** If analyzing multiple interviews from same candidate:
- Show improvement trajectory (ratings progression)
- Identify which feedback has been implemented
- Note patterns that persist vs. new patterns
- Provide evolved recommendations for next session
 
## Output Format
 
The output should be a well-organized document with:
1. Executive Summary (strengths and areas for improvement)
2. Detailed Feedback Section (organized by dimension, with examples)
3. Interview Scorecard (ratings and severity matrix)
4. Next Steps / Recommended Actions
 
## Key Instructions
 
These principles were validated across real interview analyses:
 
### Core Principles
- **Be constructive**: Frame feedback as coaching, not criticism. Every weakness should be paired with a solution pathway
- **Be specific**: Use direct quotes or specific moments from the interview with timestamps. "You spoke quickly when discussing your manager" beats "Communication could be better"
- **Be balanced**: Acknowledge strengths alongside growth areas. Aim for ratio of 2-3 strengths per major growth area
- **Be actionable**: For each concern, suggest concrete next steps. Not "Improve pacing" but "Record yourself answering at 20% slower pace"
- **Calibrate severity carefully**: 
  - **Critical**: Fundamentally impacts hiring decision (e.g., speaking so fast interviewer can't follow, providing only vague answers)
  - **Major**: Noticeable improvements needed (e.g., occasional filler words, weak goal articulation)
  - **Minor**: Polish/optimization (e.g., one sentence ending pattern, word choice refinement)
 
### Feedback Quality Checklist
- ✓ Every feedback section includes specific timestamp (00:XX:XX format)
- ✓ At least 2-3 concrete examples with quotes per dimension
- ✓ For every critical area, provide specific daily practice routine
- ✓ Show improvement trajectory if analyzing multiple sessions
- ✓ Include "What went well" before "Areas to improve" in each section
- ✓ Provide 1-5 score with clear narrative connection
- ✓ Include comparison to real interviewer feedback if available
 
### Special Handling
- **Multiple sessions from same candidate**: Track progression, highlight implemented improvements, note persistent patterns
- **Behavioral interviews**: Strong emphasis on communication, self-awareness, business acumen
- **Technical interviews**: Focus on code quality, problem-solving, system design (if applicable)
- **Mixed format interviews**: Adapt dimensions to what was actually discussed
- **Poor audio quality**: Flag this, note impact on evaluation, adjust recommendations accordingly
- **Very long interviews (45+ min)**: May need more detailed breakdown by section; can be condensed if candidate requests
 
## Technical Notes
 
- Transcription may have minor errors; flag unclear sections in feedback
- If audio quality is poor, note this impact on evaluation
- For interviews with code, reference specific code blocks in feedback
- Maintain professionalism and confidentiality of interview content
 
## Common Patterns to Watch For
 
Based on real interview analysis, these patterns frequently appear:
 
### Communication Patterns
- **Speaking pace**: Most common critical issue. Fast delivery undermines strong content
- **Sentence endings**: Filler words ("yeah", "so") at sentence end weaken conclusions
- **Verbal tics**: "Like", "basically", "I mean" scattered through answers
- **Trailing off**: Incomplete thoughts before moving to next point
 
### Specificity Patterns
- **Rating vagueness**: "Very good" vs. specific "8/10 because..."
- **Metric avoidance**: Describing impact generally vs. with numbers
- **Experience breadth vs. depth**: Spreading across many projects vs. one deep story
 
### Self-Awareness Patterns
- **Growth mindset**: Honest about limitations + implemented solutions (strength)
- **Deflection**: Acknowledging gaps without owning solutions (weakness)
- **Positivity framing**: "I'm working on that" vs. "That's hard" (strength)
 
### Relationship Communication
- **Respect shown**: Positive framing of past managers/teammates (strength)
- **Blame shifting**: Criticizing others (critical weakness)
- **Vague references**: Not showing understanding of working relationships
 
### Strategic Thinking
- **Business acumen**: Connecting technical decisions to customer/revenue impact
- **Execution vs. strategy**: Showing growth from "do what I'm told" to "think strategically"
- **ROI awareness**: Considering value before implementation
 
### Interview Technique
- **Clarifying questions**: Asking before solving (strength)
- **Following interviewer lead**: Adapting vs. forcing pre-set narrative
- **Engagement**: Showing genuine interest vs. transactional tone
 
