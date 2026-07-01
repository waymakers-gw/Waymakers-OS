[README.md](https://github.com/user-attachments/files/29534047/README.md)
# Client Meeting Digest — Setup Guide

This sends you a daily email listing which client meetings are overdue,
due soon, or not yet scheduled, based on your Notion "Client Meeting
Tracker" database.

## What you need

1. A free GitHub account (if you don't already have one).
2. A Notion **internal integration** token with access to your workspace.
3. An email account that supports SMTP app passwords (Gmail works well
   and is free).

## Step 1 — Create a Notion integration

1. Go to https://www.notion.so/profile/integrations
2. Click "New integration", give it a name like "Meeting Digest Bot".
3. Copy the **Internal Integration Secret** — this is your `NOTION_TOKEN`.
4. Open the "Client Meeting Tracker" database in Notion.
5. Click the "..." menu → "Connections" → connect your new integration
   (this grants it read access to that specific database).
6. Copy the database ID from its URL:
   `https://www.notion.so/<workspace>/<DATABASE_ID>?v=...`
   It's the 32-character string before the `?v=`.

## Step 2 — Set up an app password for email (Gmail example)

1. Turn on 2-Step Verification on your Google account if not already on.
2. Go to https://myaccount.google.com/apppasswords
3. Create an app password (choose "Mail" as the app).
4. Copy the 16-character password generated — this is your
   `SMTP_PASSWORD` (not your normal Gmail password).
5. Your `SMTP_HOST` is `smtp.gmail.com`, `SMTP_PORT` is `587`,
   `SMTP_USER` is your Gmail address.

(If you use Outlook/Office365 instead: SMTP_HOST is
`smtp.office365.com`, port `587`, and you'll need an app password from
your Microsoft account security settings.)

## Step 3 — Create the GitHub repo

1. Create a new **private** GitHub repository.
2. Upload these two files, keeping the folder structure:
   - `digest.py`
   - `.github/workflows/digest.yml`
3. Go to the repo's **Settings → Secrets and variables → Actions** and
   add these repository secrets:

   | Secret name          | Value                                      |
   |-----------------------|---------------------------------------------|
   | `NOTION_TOKEN`        | the integration secret from Step 1          |
   | `NOTION_DATABASE_ID`  | the database ID from Step 1                 |
   | `SMTP_HOST`           | e.g. `smtp.gmail.com`                       |
   | `SMTP_PORT`           | `587`                                       |
   | `SMTP_USER`           | your sending email address                  |
   | `SMTP_PASSWORD`       | the app password from Step 2                |
   | `DIGEST_TO`           | the email address you want the digest sent to |

## Step 4 — Test it

1. Go to the **Actions** tab in your repo.
2. Click "Client Meeting Digest" → "Run workflow" to trigger it manually.
3. Check your inbox. If it fails, click into the run to see the error
   log — most issues are a missing secret or the integration not being
   connected to the database.

## Schedule

By default this runs daily at 07:00 UTC (set in `digest.yml`), which is
roughly 5-6pm Melbourne time depending on daylight saving — adjust the
`cron` line to suit. Cron schedules are always in UTC.

## Adjusting what counts as "due soon"

In `digest.yml`, you can add an optional `DUE_SOON_DAYS` secret (e.g. set
to `3` or `7`) to change the lookahead window. Defaults to 5 days.

## Keeping the tracker accurate

This script only reads what's already in the "Client Meeting Tracker"
Notion database — it doesn't update it automatically. After each
meeting, update that meeting's "Last Meeting Date" and "Next Due Date"
rows so the digest stays accurate. (A future improvement could automate
this calculation too — ask Claude if you'd like that built.)
