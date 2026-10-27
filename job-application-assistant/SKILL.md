---
name: "job-application-assistant"
description: "Find, tailor and apply to jobs end to end: tailored 1-page resumes and cover letters from Google Drive, Notion tracking, per-job Desktop folders, and Chrome form filling with approval before submit."
---

# Job Application Assistant

Helps the user run a full job-application pipeline: find roles that fit, tailor a one-page resume and a cover letter for each, fill the application forms in their browser, and keep a complete record of everything sent.

## Ground rules (never skip)

1. **Never invent facts.** Every resume bullet, cover-letter sentence and form answer must come from the user's own resumes, documents or words in chat. If a form asks something you don't know (salary, sponsorship, years with a tool, personal essays), ask the user. Do not pad with plausible-sounding details (company size, team size, metrics, tools they never mentioned).
2. **Keep the user's voice.** For free-text questions, prefer the user's dictated answer, cleaned up for grammar only. When a form says answers must be in the applicant's own words, do not write the answer yourself: ask the user to dictate it, then tidy grammar.
3. **Approval before submit.** Fill and verify forms freely, but only click Submit/Apply after the user has said yes, either per application or with an explicit standing approval for the batch ("submit each once it's filled and checked").
4. **No account creation, no passwords.** If a portal requires creating an account or signing in with a password, skip it and tell the user; they can apply there themselves.
5. **No emails sent on their behalf without approval.** If an employer asks for an emailed application, draft it and let the user send it (or send only after explicit approval with a connected mail tool).
6. **Privacy-preserving defaults.** Decline SMS/marketing opt-ins and "follow company" checkboxes. Voluntary demographic surveys: fill only what the user has explicitly told you; otherwise leave blank.

## First-run setup (ask once, then reuse)

Collect and confirm with the user:
- Connections: Google Drive (resumes), Notion (tracker), Claude in Chrome (forms), Claude desktop app linked to their computer (Desktop folders). Check what is connected; suggest connecting what's missing.
- Target roles, seniority, locations (and preferences, e.g. a province or remote), daily goal.
- Standard answers: legal name, email, phone, city/address, LinkedIn, portfolio/GitHub, work authorization, sponsorship need, notice/start date, pronouns and any demographic answers they want given, how to answer salary questions (e.g. "within the posted range"; a fallback number when no range is posted).
- Education results (GPA, honours), so recurring questions don't need re-asking.
Store these in a working file for the session (not in the skill).

## Pipeline

### 1. Gather source material
- Find all resume versions in Google Drive (search for "resume"/"CV"); read every one. Build a master inventory of jobs, dates, bullets, projects, skills, courses and awards, using only what appears in the documents.
- Pick the best-formatted resume (.docx) as the layout template.

### 2. Find roles
- Search job boards (LinkedIn, company career pages) for roles matching their skills and location preferences. Favour postings that are recent, entry/junior-appropriate if relevant, and have a direct ATS form (Ashby, Greenhouse, Lever, Rippling, Teamtailor, LinkedIn Easy Apply).
- For each: company, role, link, location, work mode, posted salary, 1-line fit note (including honest gaps, e.g. "asks 2+ yrs").
- Show the list; let the user drop any before tailoring.

### 3. Tailor documents (per job)
- Group roles into tracks (e.g. full-stack, frontend, IT support, analyst) and make one tailored resume spec per track, then lightly adjust per company.
- Resume: edit the template .docx with python-docx (reuse existing paragraph styles by cloning template paragraphs, keep hyperlinks intact), reorder/choose bullets for the role, rewrite the summary for the track. Convert to PDF with LibreOffice (`soffice --headless --convert-to pdf`) and check `pdfinfo` shows **Pages: 1**; trim bullets until it does. Check right-aligned dates don't collide with text.
- Cover letter: 3–5 short paragraphs, specific projects and outcomes from the inventory, why this company (only from the posting), no generic filler. Export PDF.
- Name files `Firstname_Lastname_Resume_<Company>.pdf` and `Firstname_Lastname_Cover_Letter_<Company>.pdf` (company name without spaces/punctuation).

### 4. Records
**Notion database** (create fresh if the user asks) with properties: Role (title), Company, Status (To Review / Applied / Interview / Rejected / Offer / Closed / Skipped), Location, Work Mode, Track, Job Link, Date Found, Date Applied, Salary (posted range + what was entered), Fit Notes, Resume Version, Cover Letter (checkbox), Desktop Folder, Follow-up Date (applied + 14 days).

**Desktop folder**: `~/Desktop/Job Applications/<YYYY-MM-DD> – <Company> – <Role>/` containing:
- `resume.pdf` (+ .docx), `cover-letter.pdf`
- `job-posting.md` — full posting text with link, location, salary, date saved
- `notes.md` — fit notes, status, and after submitting: timestamp, confirmation message text, and every answer entered on the form verbatim
- `screenshots/` when available
Write files in the workspace, then copy them to the computer with the device file-commit tool.

### 5. Fill forms (Claude in Chrome)
- Open each form in its own tab; upload files from a local workspace path (upload tools may reject paths in output/mounted folders; copy to a plain working dir first).
- Fill everything, then re-read the whole form (page text + screenshots) before asking to submit.
- Report to the user in one consolidated message: which forms are ready, the salary entered on each, and every question only they can answer.

### 6. Submit and log
After approval: submit, confirm the success message, then immediately update notes.md (answers + confirmation), set Notion Status = Applied and Date Applied, and commit files to the Desktop.

## ATS field-handling notes

- **Ashby**: Yes/No are toggle buttons. Toggles set early sometimes don't register; before submitting, re-click each toggle (other option, then the intended one). Location fields are autocompletes: click the field, type the city, pick the suggestion. If the submit shows "Missing entry for required field", fix that field and resubmit. Use form-input tools rather than raw JS value setting for textareas so React state updates.
- **Greenhouse** (direct or embed `job-boards.greenhouse.io/embed/job_app?for=<board>&token=<id>`): dropdowns are react-select; click, type, press Enter or click the option, then verify via `.select__single-value`. Typing "Yes" can match a longer option; check the selected text. Phone country and Location (City) need an actual suggestion selected. If a company's careers page embed won't load, open the Greenhouse embed URL directly.
- **LinkedIn Easy Apply**: multi-page modal; review page before Submit; uncheck "Follow company". If the file input isn't in the DOM, it's created on demand — intercept `HTMLInputElement.prototype.click` to capture it, label it, then upload.
- **Rippling**: upload the resume first (it autofills), then cover letter, then selects. Hidden file inputs can be made visible via JS so they can be targeted.
- **Closed postings**: if the job URL redirects to the company's job list, mark Notion Status = Closed and move on.
- **Rate limits / tool errors**: if an element-finding tool is rate-limited, fall back to reading the page's interactive elements, JS queries and coordinate clicks.

## Wrap-up
End each batch with: number submitted today vs goal, list of submitted companies, what's ready awaiting approval, what's blocked (account portals, emails to send, closed postings), and open questions for the user.