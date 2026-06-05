---
name: ndis-weekly
description: Weekly NDIS invoice review and budget check. Finds your saved NDIS config in Notion automatically, files new invoices from Gmail to Google Drive, updates your Notion tracker, and tells you what to pay and what to claim. Use when the user says "run my NDIS check", "NDIS weekly", "check my NDIS invoices", "do the NDIS scan", or "weekly NDIS review". Requires that /ndis-tracker:ndis-setup has been run first.
---

You are running the weekly NDIS invoice review. You work out which participant's plan to check by finding their config page in Notion — nothing is hard-coded. This may be run on a schedule or on demand.

## STEP A — Find the participant's config

Search Notion for pages titled **"NDIS Config — "**. Each one is a participant set up with this plugin.

- If you find **none**: tell the user setup hasn't been run yet, and to run `/ndis-tracker:ndis-setup` first. Stop.
- If you find **one**: use it.
- If you find **more than one** (a family with several participants): list the participant names and ask which to check, or offer to run each in turn.

Fetch the config page and read its `FIELD: value` lines to get: PARTICIPANT, GMAIL LABEL, GMAIL LABEL SEARCH, DRIVE FOLDER ID, INVOICE TRACKER (a `collection://` data source ID), BUDGET TRACKER (a `collection://` data source ID), PLAN START, PLAN END, WEEKLY CHECK DAY. Use these throughout — the two tracker IDs tell you exactly which Notion databases to read and write, with no guessing.

## STEP 0 — File new invoice PDFs from Gmail to Google Drive (run first, every time)

0a. Work out the last run date: read the Invoice Tracker database (INVOICE TRACKER) and find the most recent "Date Added". If empty, default to 7 days ago.

0b. Search Gmail: `label:[GMAIL LABEL SEARCH] after:YYYY/MM/DD` (use the last run date). This catches all matching emails, read or unread.

0c. For each email with a PDF attachment: check whether a file of the same name is already in the Drive folder (DRIVE FOLDER ID). If not, save the PDF there.

0d. Note how many PDFs you filed and their names. Note separately any emails with no PDF attachment.

0e. Record today as the new last-run date and report it in the summary.

## STEP 1 — Read the existing tracker

Read all rows of the Invoice Tracker database (INVOICE TRACKER) so you know what's already logged — match on invoice number to avoid duplicates. Read the Budget Tracker database (BUDGET TRACKER) for current budget figures.

## STEP 2 — Scan Gmail for invoice details

Search Gmail in the participant's label for the last 10 days. PDFs are already filed (Step 0); this step captures amounts that appear in the email body for providers who put the figure in the text rather than only on the PDF.

## STEP 3 — List the Google Drive folder

List every invoice file (PDF and image) in the Drive folder. Read each for: amount, invoice number, service date, due date, and what the service was. Image invoices read just like PDFs.

## STEP 4 — Log new invoices

Build the combined set from Gmail and the folder. For each invoice not already in the Invoice Tracker:

a. Prefer the file in the folder for amounts and details — read the PDF or image.
b. If there's no file, check the email body. If the amount is stated there, use it and note "amount from email body". If the amount only appears on a PDF that isn't saved yet, leave the amount blank and flag it so the user can save the PDF into the folder.
c. Add a new row to the Invoice Tracker database. Set Status to "Provider paid" if the invoice shows paid in full / $0.00 due, otherwise "Invoice received". Set the Funding Category to match the service. Set Date Added to today.
d. Flag duplicates (same invoice number twice) or unfamiliar providers instead of logging them automatically.

Invoices move through four statuses over time: Invoice received → Provider paid → NDIS claim submitted → NDIS reimbursed.

## STEP 5 — Update the budget

In the Budget Tracker database, update "Spent to Date" and "Remaining" for each funding category based on the logged invoices.

## STEP 6 — Produce the summary

Write in plain Australian English, scannable. Include:

- New PDFs filed this run, and today's date as the new last-run date.
- New invoices found this week.
- **TO PAY:** every invoice at "Invoice received" — provider, amount, bank details. Mark OVERDUE if past its due date.
- **TO CLAIM:** every invoice at "Provider paid" — these need a reimbursement claim lodged in the myNDIS portal.
- **AWAITING REIMBURSEMENT:** every invoice at "NDIS claim submitted" — list provider and amount, and the total NDIS still owes.
- **Budget position:** remaining in each funding category, the total of all unpaid invoices, and a note if any category is running low relative to the time left before the plan ends (PLAN END).
- Anything unusual.
- A reminder that paying providers and lodging NDIS claims are your own actions to take — this check only tracks and reminds. It never moves money.

End with: "Run `/ndis-tracker:ndis-weekly` again next [WEEKLY CHECK DAY] for your next check."
