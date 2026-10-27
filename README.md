# Job Application Assistant (Claude skill)

A Claude skill that runs your job applications end to end:

- Reads your resumes from **Google Drive** and builds a tailored **one-page resume** and **cover letter** for each job
- Finds roles that fit your background and location
- Tracks every application in a **Notion** database (status, link, salary, follow-up date)
- Creates a folder per application on your **Desktop** with the posting, documents, and every answer entered on the form
- Fills application forms in **Chrome** (Ashby, Greenhouse, LinkedIn Easy Apply, Rippling and more)

**Ground rules built in:** it never invents facts, keeps your own voice for written answers, and **always asks you before submitting** anything. It skips portals that require creating an account.

## Install

**Claude app (claude.ai / desktop)**
1. Click **Code → Download ZIP** on this page.
2. In Claude, go to **Customize → Skills**, click **+ → Create skill → Upload a skill**, and upload the ZIP.

**Claude Code**
```bash
git clone https://github.com/isabelagbu/job-application-assistant
cp -r job-application-assistant/job-application-assistant ~/.claude/skills/
```

## Setup
Connect these in Claude before your first run:
- Google Drive (put your resume versions there)
- Notion
- Claude in Chrome (browser extension)
- Claude desktop app linked to your computer (only needed for the Desktop folders)

Then just ask: *"Help me apply to jobs."* Claude will ask for your contact details, work authorization, salary preferences, and target roles on the first run.
