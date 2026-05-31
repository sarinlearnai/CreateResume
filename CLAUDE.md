# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this project is

This is **not a software codebase** — it is a resume-tailoring workspace. Its purpose is to
generate one-page, job-specific resumes (`.docx`) for **a single candidate** from one
authoritative CV, tailored to a given job description. The candidate's personal data lives
only in the CV PDF under `reference/` (untracked in git) and is read at runtime — it is
never hardcoded into any tracked file.

There is no build, test, or lint step. The "code" is the skill in `skills/` plus the
Python that skill emits at runtime to write the `.docx`.

## Layout

- `prompts/` — natural-language task specs that drive the work (e.g.
  `prompt for resume-builder-skill.txt`, which defined the resume-builder skill).
- `reference/` — the source-of-truth inputs (**untracked in git**):
  - the candidate's CV PDF — the **standard CV**. It is both the visual template and the
    *only* authoritative source of credentials.
  - `<Role>.txt` files (e.g. `DevOps Engineer.txt`, `Software Engineer.txt`,
    `Veeva Configurator.txt`) — job descriptions to tailor against.
- `skills/resume-builder-skill.md` — the resume-builder skill (read this before doing any
  resume work).
- Generated resumes are written to the **`output/` folder** (**untracked in git**) as
  `output/<CandidateName>CV-<file-name>.docx`, where `<CandidateName>` is read from the CV
  (the folder is created if it does not exist).

## Skills

- **`resume-builder-skill`** (`skills/resume-builder-skill.md`) — the registered skill for
  this workspace. Create a tailored, one-page resume (`.docx`) for a given job description,
  using the candidate's standard CV as the template and single source of truth for
  credentials.
  - **Invoke it whenever** the user asks to build, tailor, or generate a resume for a named
    job-description file. Read the skill in full before doing any resume work — it holds the
    credential-source guidance, hard rules, tailoring approach, formatting spec, and
    `python-docx` helpers. All personal data is read from the CV at runtime.

## Core workflow

To build a tailored resume, follow `skills/resume-builder-skill.md`. In short:

1. The user names a job-description file; match it in `reference/` (case-insensitive,
   `.txt` optional). If no match, list `reference/` and ask — never guess.
2. Re-read the candidate's CV PDF in `reference/` to confirm credentials.
3. Tailor by **re-emphasising and rephrasing** the CV's real content to the JD.
4. Emit a Python script using `python-docx` to write
   `output/<CandidateName>CV-<file-name>.docx` (creating the `output/` folder if needed).

## Non-negotiable constraints (from the project's prompt and skill)

- **Truthful only.** Never invent employers, dates, titles, degrees, certifications,
  tools, or years of experience. Everything must trace back to the candidate's CV.
  If a JD asks for something the candidate lacks, surface the closest genuine strength
  instead.
- **One page**, clear and concise.
- **Preserve the original's understated tone** ("Good understanding of…", "Experienced
  in…") — no added hype.
- **Match the original's visual style**: tan/khaki shaded section-header bars
  (~`C4B998`), muted-blue job-title sub-headings (~`4F81BD`), a two-column contact block,
  compact spacing.
- **Identity/contact details verbatim** from the CV.

## Environment notes

- Platform is Windows; the default shell is PowerShell.
- Python 3.14 is available but **`python-docx` is not installed** — install it
  (`python -m pip install python-docx`) before generating a `.docx`.
