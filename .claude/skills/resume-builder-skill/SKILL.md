---
name: resume-builder-skill
description: Create a new tailored, one-page resume (.docx) for a given job description, using the candidate's standard resume in the reference folder as the template and the single source of truth for credentials. Use when the user asks to build/tailor a resume for a named job-description file.
---

# Resume Builder Skill

> **The full skill lives in the project at `skills/resume-builder-skill.md`** — that file is
> the single source of truth. This entry only registers the skill with Claude Code so it can
> be invoked as `/resume-builder-skill` (or via the Skill tool). Do not duplicate the
> instructions here.

When this skill is invoked, **read `skills/resume-builder-skill.md` in full and follow it
exactly.** It contains the inputs, hard rules, credential-source guidance, tailoring
approach, formatting spec, generation steps, and `python-docx` helpers. All personal data
is read from the CV PDF in `reference/` at runtime — nothing personal is stored in the
skill files.

In brief:

1. The user names a job-description file; match it in `reference/` (case-insensitive, `.txt`
   optional). If no match, list `reference/` and ask — never guess.
2. Re-read the CV PDF in `reference/` to confirm credentials (the only facts you may use).
3. Tailor by **re-emphasising and rephrasing** the CV's real content to the JD — never invent.
4. Emit a Python script using `python-docx` to write
   `output/<CandidateName>CV-<file-name>.docx` (candidate name read from the CV; create the
   `output/` folder if it does not exist), matching the original CV's one-page visual style.
