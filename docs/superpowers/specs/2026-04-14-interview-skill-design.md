# Interview Skill Design

**Date:** 2026-04-14  
**Status:** Approved  
**Type:** Claude Code Skill (single skill, distributable)

---

## Overview

A Claude Code skill (`/interview`) that conducts AI-powered technical mock interviews based on the user's resume and job posting. Free-form conversation style — the user answers questions naturally, and when done types `/done` to generate a structured report.

---

## Target Users

Developer community (public distribution). Installation should be simple — single skill file, no MCP server or external dependencies required.

---

## Session Flow

```
/interview [file paths...] or no args
       ↓
[1. Input Collection]
  - If file paths provided → Claude reads them (resume, JD)
  - If no args → prompt user to paste resume and JD content

       ↓
[2. Context Analysis]
  - Extract tech stack and experience from resume
  - Extract required skills and position from JD
  - Set interview direction accordingly

       ↓
[3. Free-form Interview]
  - Claude acts as interviewer
  - User answers → Claude gives brief feedback + next question
  - One question at a time
  - User types /done to end session

       ↓
[4. Report Generation]
  - Display summary in conversation
  - Save ./interview-report-YYYY-MM-DD-HH-MM.md to current directory
```

---

## Skill File Structure

```
interview-skill/
├── skills/
│   └── interview.md        # Skill body (interviewer rules + flow control)
├── docs/
│   └── superpowers/
│       └── specs/
│           └── 2026-04-14-interview-skill-design.md
├── README.md               # Installation guide + usage examples
└── .gitignore
```

---

## Skill File Internal Structure (`skills/interview.md`)

```
---
name: interview
description: AI technical interviewer — free-form mock interview based on resume and JD
---

[Trigger conditions]
[Input parsing rules — file path vs paste]
[Interviewer persona & behavior rules]
[/done detection → report generation instructions]
[File save rules]
```

---

## Interviewer Persona & Behavior Rules

- Professional but not stiff — direct and sharp like a real technical interview
- Follow-up questions when answers are shallow ("Can you explain that in more detail?")
- Acknowledge good answers before moving on
- Generate questions based on resume tech stack and JD requirements
- Mix in CS fundamentals (data structures, networking, OS, etc.)
- Ask only one question at a time

**Question generation priority:**
1. Tech stack listed in resume
2. Requirements from JD
3. CS fundamentals relevant to the role

---

## Input Handling

| Method | How |
|--------|-----|
| File path | `/interview resume.pdf jd.txt` → Claude reads files |
| Paste | `/interview` → Claude prompts user to paste content |
| Both | Either method works; can mix (e.g., file for resume, paste for JD) |

Supported file types: `.pdf`, `.txt`, `.md`, `.docx` (anything Claude Code can read)

---

## Report Structure

Generated on `/done` command. Saved as `./interview-report-YYYY-MM-DD-HH-MM.md`.

1. **Q&A Full Log** — each question, user's answer, and model answer
2. **Areas to Improve** — weak CS topics identified during session
3. **Overall Assessment** — strengths and weaknesses summary

---

## Usage Examples

```bash
# With file paths
/interview resume.pdf jd.txt

# Paste mode
/interview
→ "Please paste your resume content"
→ (user pastes)
→ "Now paste the job description"
→ (user pastes)
→ Interview begins

# End session
/done
→ Report generated and saved
```

---

## Out of Scope

- No authentication or user accounts
- No cloud storage — local file only
- No multi-session history tracking
- No MCP server dependency
