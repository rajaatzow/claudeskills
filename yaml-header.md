At the top of your SKILL.md file, you write a block of metadata between --- lines called YAML frontmatter. This tells Claude when to activate your Skill.

Here’s what it looks like in practice:

---
name: csv-cleaner
description: Transforms messy CSV files into clean spreadsheets 
  with proper headers. Use this skill whenever the user says 
  'clean up this CSV', 'fix the headers', 'format this data', 
  or 'organise this spreadsheet'. Do NOT use for PDFs, Word 
  documents, or image files.
---
