---
name: resume-builder-skill
description: Create a new tailored, one-page resume (.docx) for a given job description, using the candidate's standard resume in the reference folder as the template and the single source of truth for credentials. Use when the user asks to build/tailor a resume for a named job-description file.
---

# Resume Builder Skill

Generates a tailored, one-page resume in Microsoft Word (`.docx`) format for a specific
job description, using **the candidate's standard CV** (the PDF in the `reference/` folder)
as both the visual template and the authoritative source of credentials.

> **Privacy:** this skill file intentionally contains **no personal data**. The candidate's
> identity, contact details, employment history, and all other credentials live **only** in
> the CV PDF under `reference/` (which is not tracked in git). Read them from that PDF at
> runtime — never hardcode them here.

## Inputs

1. **Job-description file name** — supplied by the user (e.g. `DevOps Engineer`,
   `Software Engineer`, `Veeva Configurator`). **This input is mandatory.** If the skill
   is invoked without a job-description file name, **stop and explicitly ask the user for
   it, and do not perform any other step (do not read the CV, do not install anything, do
   not generate a file) until the user provides it.** Never assume, reuse a name from
   earlier in the conversation, or guess. Once provided, match it to a file in the
   `reference/` folder. Accept the name with or without the `.txt` extension and match
   case-insensitively. If no matching file is found, list the available files in
   `reference/` and ask the user to pick one. **Do not** proceed with a guessed file.
2. **Standard resume** — the candidate's CV PDF in `reference/` (fixed; always the
   template and the only source of credentials).

## Output

- A single file written to the **`output/` folder** in the project root:
  `output/<CandidateName>CV-<file-name>.docx`, where `<CandidateName>` is the candidate's
  name read from the CV with spaces removed, and `<file-name>` is exactly the value the
  user provided in step 1 (e.g. `output/<CandidateName>CV-DevOps Engineer.docx`).
- **Create the `output/` folder if it does not already exist** before writing.

## Hard rules (read before generating)

- **One page only.** The finished resume must fit on a single page. Keep it clear and
  concise — trim, don't pad.
- **Truthful and verifiable.** Every claim must be supported by the standard CV. You may
  *re-emphasize, reorder, and rephrase* existing experience to align with the job
  description, but you must **never invent** employers, dates, degrees, certifications,
  job titles, tools, or years of experience that are not in the CV.
  If the job description asks for a skill the candidate does not have, do not claim it —
  instead surface the closest genuine, transferable strength.
- **Preserve sentiment & tone.** Match the measured, professional, understated voice of
  the original CV (e.g. "Good understanding of…", "Experienced in…"). Do not introduce
  hype or superlatives that are absent from the source.
- **Preserve identity & contact details verbatim** as they appear in the CV (read them at
  runtime; do not paraphrase the name, phone, email, or address).

## Source of credentials (read at runtime — nothing is hardcoded here)

The CV PDF in `reference/` is the complete, authoritative record. **Re-read it at runtime**
and extract everything you need directly from it:

- **Header / contact** — name, phone, email, work-permit line, and address (use verbatim).
- **Personal summary** — the original summary paragraph.
- **Professional experience** — every employer, title, location, date range, and bullet.
- **Key competencies** — the listed competencies.
- **Academic qualifications & certification** — degrees and certifications.

Do not rely on any cached copy of these facts; the PDF is the single source of truth.

## Tailoring approach

1. **Read the job description** from `reference/<file-name>.txt` and extract its key
   themes, required skills, and the role title.
2. **Map, don't fabricate.** For each job-description theme, find the genuine evidence in
   the CV that best speaks to it, and lead with that. Examples of legitimate re-framing:
   - JD wants "data correctness / data quality" → emphasise any data-cleansing,
     data-quality / testing, and "attention to detail" experience in the CV.
   - JD wants "cross-functional communication" → emphasise coordinating teams across
     geographies and reporting to executive leadership.
   - JD wants "software development / configuration" → emphasise relevant degrees,
     certifications, teaching of programming & SDLC, and "build & test tailored products."
3. **Rewrite the summary** (2–4 lines) so it opens by positioning the candidate toward the
   target role, while staying within the truthful scope above and keeping the original tone.
4. **Adjust the Key Competencies** list to foreground the items most relevant to the JD
   (reorder / lightly rephrase the genuine ones; you may add a competency only if it is
   clearly evidenced by the experience already listed).
5. **Keep experience entries factual** — same employers, titles, and dates; you may
   re-order or re-emphasise bullets to match the JD, but each bullet must remain true to
   the original.
6. **Optionally add a short "Relevant Skills" line** drawn only from genuine skills
   evidenced in the CV (e.g. CRM, data quality/testing, programming fundamentals,
   stakeholder management) if it helps ATS alignment — never list a tool the candidate
   hasn't demonstrably used.

## Formatting (match the original CV's look)

Reproduce the visual style of the CV PDF in `reference/`:
- **Name** large and bold at the top.
- A two-column contact block: phone / email / work-permit on the left, address on the
  right.
- Section headers (PERSONAL SUMMARY, PROFESSIONAL EXPERIENCE, KEY COMPETENCIES,
  ACADEMIC QUALIFICATIONS & CERTIFICATION) rendered as **bold uppercase text on a
  tan/khaki shaded bar** (approx. hex `C4B998`).
- Job-title sub-headings in a **muted blue** (approx. hex `4F81BD`).
- Body text and bullet points in black, compact spacing to stay within one page.
- Use a clean sans/serif consistent with the original (Calibri is a safe default).

## Generation steps

0. **Confirm the job-description file name was provided.** If it was not, stop here and ask
   the user for it; do not proceed to any later step until they answer.
1. **Resolve the input file** in `reference/` (case-insensitive, optional `.txt`).
2. **Re-read** the CV PDF in `reference/` to extract the credentials (see "Source of
   credentials" above).
3. **Read** the job-description file and derive the tailoring as above.
4. **Ensure `python-docx` is available** (used to write the `.docx`):
   - Check: `python -c "import docx"`
   - If missing, install: `python -m pip install python-docx`
5. **Generate the `.docx`** with a Python script using `python-docx`. Use shaded
   paragraph backgrounds for the section-header bars (via a `w:shd` element added to the
   paragraph properties), a borderless 2-column table for the contact block, bold/colored
   runs for headings, and tight paragraph spacing. Set page margins (~0.7") and font size
   (~10–10.5pt body) so the content fits on one page.
6. **Ensure the `output/` folder exists** (create it if missing), then **write** the file
   to it as `output/<CandidateName>CV-<file-name>.docx`.
7. **Verify**: confirm the file was created, re-state to the user which JD was used, and
   confirm that every line maps back to the standard CV (flag anything the JD wanted that
   could not be truthfully claimed).

## Reference: python-docx helper for the shaded section-header bar

```python
from docx.oxml.ns import qn
from docx.oxml import OxmlElement

def shade_paragraph(paragraph, fill_hex="C4B998"):
    """Add a solid background fill to a paragraph (the tan header bar)."""
    pPr = paragraph._p.get_or_add_pPr()
    shd = OxmlElement('w:shd')
    shd.set(qn('w:val'), 'clear')
    shd.set(qn('w:color'), 'auto')
    shd.set(qn('w:fill'), fill_hex)
    pPr.append(shd)
```

Use `RGBColor(0x4F, 0x81, 0xBD)` for the blue job-title sub-headings and
`RGBColor(0, 0, 0)` for body text. Keep `paragraph_format.space_after` small (e.g. 2–4 pt)
to preserve the one-page constraint.
