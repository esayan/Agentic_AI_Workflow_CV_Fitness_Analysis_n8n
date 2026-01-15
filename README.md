📄 Simple CV Fit (Drive + OpenAI + Email)
👉 klasör yolu (öneri):
workflows/simple-cv-fit-drive-openai-email/README.md
# Simple CV Fit – Drive + OpenAI + Email (n8n)

This n8n workflow automatically evaluates **candidate CV fit** against a given **job profile** using OpenAI, then sends email notifications only for candidates above a defined fit threshold.

It is designed to be:
- Simple
- Transparent
- Human-readable
- Easy to adapt for real HR pipelines

---

## What this workflow does

1. Downloads a **job profile document** from Google Drive
2. Extracts the job description text
3. Lists all CV files from a Google Drive folder
4. Processes CVs **one by one**
5. Uses **OpenAI** to:
   - Compare CV vs job profile
   - Score fit on a scale of 1–10
   - Provide reasoning in bullet points
6. Parses OpenAI response as structured JSON
7. Sends an **email notification** for candidates above a fit threshold
8. Skips candidates below the threshold (no noise)

---

## High-level architecture

Manual Trigger
↓
Set Variables
↓
Download Job Profile (Drive)
↓
Extract Profile Text
↓
List CV Files (Drive Folder)
↓
Loop CVs (1-by-1)
↓
Download CV (PDF)
↓
Extract CV Text
↓
OpenAI Fit Evaluation
↓
Parse JSON Output
↓
IF Fit >= Threshold
├─ YES → Send Email
└─ NO → Skip

---

## Prerequisites

- n8n (Cloud or self-hosted)
- Google Drive account
- OpenAI API key
- Microsoft Outlook / Office 365 account

---

## Required credentials

Configure the following credentials in n8n:

- **Google Drive OAuth2**
- **OpenAI API**
- **Microsoft Outlook OAuth2**

---

## Configuration (Variables)

This workflow uses a simple variable-based configuration.

### Variables used

| Variable name        | Description |
|---------------------|-------------|
| `CV_FOLDER_ID`      | Google Drive folder containing CV PDFs |
| `PROFILE_FILE_ID`   | Google Drive file ID (job profile) |
| `FIT_THRESHOLD`     | Minimum fit score (1–10) |
| `EMAIL_TO`          | Email address to notify |

Example (n8n → Set node or Variables):

```env
CV_FOLDER_ID=https://drive.google.com/drive/folders/XXXXXXXX
PROFILE_FILE_ID=https://drive.google.com/file/d/YYYYYYYY
FIT_THRESHOLD=7
EMAIL_TO=hr@company.com
OpenAI prompt logic
The model is instructed to return ONLY JSON in Turkish with the following structure:
{
  "Aday": "Candidate Name",
  "Pozisyon": "Position Name",
  "Fitlik": 8,
  "Sebepler": [
    "Relevant experience",
    "Skill alignment",
    "Industry background",
    "Education match",
    "Tool familiarity"
  ]
}
This is parsed and normalized inside the workflow.
Email behavior
Email is sent only if fit_score >= FIT_THRESHOLD
Subject includes:
Position name
Candidate name
Body includes:
Fit score
Reasoning bullets
Candidates below threshold generate no email.
Supported file formats
Job profile: .docx or .pdf
CVs: .pdf
How to import
Download workflow.json
n8n → Workflows → Import from file
Bind required credentials
Update variables (folder ID, profile ID, email)
Run manually or schedule
How to test quickly
Upload 2–3 sample CV PDFs into the Drive folder
Use a short job profile document
Set FIT_THRESHOLD=6
Trigger workflow manually
Observe email output
Common issues & tips
OpenAI JSON parsing error
→ Model must return only JSON. Prompt already enforces this.
Drive permission error
→ Ensure files are shared with the Google Drive OAuth account.
Too many emails
→ Increase FIT_THRESHOLD (7–8 recommended).
Intended use cases
Initial CV screening
Recruiter pre-filtering
Demo of AI-assisted HR workflows
Agentic AI examples in n8n
