---
name: profile-webpage-skill
description: Generate a one-page, developer-themed personal profile webpage from an already-generated resume in the output folder, and serve it on localhost for viewing in the browser. Use when the user asks to publish, display, or turn a named resume file into a webpage.
---

# Profile Webpage Skill

> **The full skill lives in the project at `skills/profile-webpage-skill.md`** — that file is
> the single source of truth. This entry only registers the skill with Claude Code so it can
> be invoked as `/profile-webpage-skill` (or via the Skill tool). Do not duplicate the
> instructions here.

When this skill is invoked, **read `skills/profile-webpage-skill.md` in full and follow it
exactly.** It contains the inputs, hard rules, content-source guidance, developer-theme
design approach, generation steps, and `python-docx` read helpers. All personal data is read
from the resume `.docx` in `output/` at runtime — nothing personal is stored in the skill
files.

**Run interactively:** always take the resume file name from the user, ask as many
clarifying design questions as needed, then present a short plan and **wait for explicit
confirmation before building or serving anything**. The user is new to webpage creation.

In brief:

1. The user names a resume file; match it in `output/` (case-insensitive; `.docx` and the
   `<CandidateName>CV-` prefix optional). If no/multiple matches, list `output/` and ask —
   never guess. Then ask design questions and confirm a plan before doing any work.
2. Read the matched `.docx` with `python-docx` (the only content you may use).
3. Build a single self-contained, developer-themed (dark background) one-page webpage at
   `output/<resume-stem>.html` (create the `output/` folder only if it does not exist), with
   every word taken verbatim from the resume — never invent links, taglines, or sections,
   and preserve the resume's understated sentiment.
4. Serve it on localhost (e.g. `python -m http.server 8000 --directory "output"`,
   run in the background) and give the user the URL.
