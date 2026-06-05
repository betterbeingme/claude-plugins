---
name: ndis-weekly
description: Weekly NDIS invoice review and budget check. Finds your saved NDIS config automatically, files new invoices from Gmail to Google Drive, updates your tracker, and tells you what to pay and what to claim. Use when the user says "run my NDIS check", "NDIS weekly", "check my NDIS invoices", "do the NDIS scan", or "weekly NDIS review". Requires that /ndis-tracker:ndis-setup has been run first.
---

You are running the weekly NDIS invoice review. You work out which participant's plan to check by finding their saved config file — nothing is hard-coded. This may be run on a schedule or on demand.

## STEP A — Find the participant's config

Search Google Drive for files whose title contains **"NDIS Config"**. Each one is a participant. Ignore any file titled "ndis-config-test" (that's a setup test file, not a real participant).

- If you find **none**: tell the user it looks like setup hasn't been run yet, and to run `/ndis-tracker:ndis-setup` first. Stop.
- If you find **one**: use it.
- If you find **more than one** (a family with several participants): list the participant names and ask which one to check — or offer to run the check for each in turn.

Read the chosen config doc. Parse the `FIELD: value` lines to get: PARTICIPANT, PLAN MANAGER, GMAIL LABEL, GMAIL LABEL SEARCH, DRIVE FOLDER ID, TRACKER SHEET, PLAN START, PLAN END, and every BUDGET line. Use these values throughout — wherever the steps below say "the folder", "the label", "the tracker", etc., use the values from the config.

## STEP 0 — File new invoice PDFs from Gmail to Google Drive (run first, every time)

0a. Work out the last run date: open the participant's tracker sheet (TRACKER SHEET, in the DRIVE FOLDER), look at the Invoice Tracker tab, and find the most recent "Date Added". If there's nothing, default to 7 days ago.

0b. Search Gmail using the label search value: `label:[GMAIL LABEL SEARCH] after:YYYY/MM/DD` (use the last run date). This catches all matching emails, read or unread.

0c. For each email with a PDF attachment: check whether a file with the same name is already in the Drive folder (DRIVE FOLDER ID). If not, save the PDF there.

0d. Note how many new PDFs you filed and their names. Note separately any emails that had no PDF attachment.

0e. Record today as the new last-run date and report it in the summary.

## STEP 1 — Read the existing tracker

Open the tracker sheet. Read the Invoice Tracker tab so you know which invoices are already logged (match on invoice number to avoid duplicates). Read the Budget Tracker tab for current budget figures.

## STEP 2 — Scan Gmail for invoice details

Search Gmail in the participant's label for emails from the last 10 days. PDF attachments are already filed (Step 0); this step captures amounts that appear in the email body for providers who put the figure in the email text rather than only on the PDF.

## STEP 3 — List the Google Drive folder

List every invoice file (PDF and image) in the Drive folder. Read each to get: amount, invoice number, service date, due date, and what the service was. Image invoices can be read for their details just like PDFs.

## STEP 4 — Log new invoices

Build the combined set of invoices from Gmail and the folder. For each invoice not already in the tracker:

a. Prefer the file in the folder for amounts and details — read the PDF or image.
b. If there's no file, check the email body. If the amount is stated there, use it and note "amount from email body". If the amount only ever appears on a PDF that isn't saved yet, leave the amount blank and flag it for the plan manager to save the PDF into the folder.
c. Add a row to the Invoice Tracker tab. Set Status to "Provider paid" if the invoice shows paid in full / $0.00 due, otherwise "Invoice received". Assign it to the right funding category based on the service.
d. Flag duplicates (same invoice number twice) or unfamiliar providers instead of logging them automatically.

Each invoice moves through four statuses over time: Invoice received → Provider paid → NDIS claim submitted → NDIS reimbursed.

## STEP 5 — Update the budget

For each funding category, update "Spent to Date" and "Remaining" in the Budget Tracker tab based on the logged invoices, using the BUDGET totals from the config.

## STEP 6 — Produce the summary

Write in plain Australian English, scannable. Include:

- New PDFs filed this run, and today's date as the new last-run date.
- New invoices found this week.
- **TO PAY:** every invoice at "Invoice received" — provider, amount, bank details. Mark OVERDUE if past its due date.
- **TO CLAIM:** every invoice at "Provider paid" — these need a reimbursement claim lodged in the myNDIS portal.
- **AWAITING REIMBURSEMENT:** every invoice at "NDIS claim submitted" — list provider and amount, and the total NDIS still owes.
- **Budget position:** remaining in each funding category, the total of all unpaid invoices, and a note if any category is running low relative to the time left before the plan ends (PLAN END).
- Anything unusual.
- A reminder that paying providers and lodging NDIS claims are the plan manager's own actions — this check only tracks and reminds. It never moves money.

End with: "Run `/ndis-tracker:ndis-weekly` again next [WEEKLY CHECK DAY] for your next check."
