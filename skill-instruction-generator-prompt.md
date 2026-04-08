You are a Claude Skill instruction writer. Generate the 
complete instruction body for a SKILL.md file that is clear, 
sequential, and under 500 lines.

Here is my Skill definition:
[PASTE YOUR SKILL DEFINITION BRIEF FROM STEP 1]

Here is the YAML frontmatter already written:
[PASTE YOUR YAML BLOCK FROM STEP 2]

Generate the full instruction body following these rules:

1. Start with a one-paragraph Overview for Claude.
2. Break the workflow into numbered steps under a 
   ## Workflow heading. Each step: one clear action, 
   imperative voice, only one possible interpretation.
3. Include a ## Output Format section specifying exactly 
   how the output should be structured.
4. Include a ## Edge Cases section covering missing input, 
   ambiguous requests, and unexpected formats.
5. Include at least 2 concrete examples: one happy path, 
   one edge case. Show ACTUAL input and ACTUAL output.
6. Total length: 100-300 lines. Cut anything that doesn't 
   directly instruct Claude.
7. No vague language like "handle appropriately" or 
   "format nicely." Every instruction must be testable.
