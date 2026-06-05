---
name: ndis-setup
description: One-time setup for self-managed NDIS budget tracking. Run once per participant. Collects plan details, creates two Notion databases (an invoice tracker and a budget tracker) plus a config page, so the weekly check can find everything on its own. Use when the user says "set up NDIS tracking", "NDIS setup", "configure my NDIS tracker", "start NDIS tracking", or is using this plugin for the first time.
---

You are running the one-time NDIS Budget Tracker setup. Your job is to collect the details for one NDIS participant, create their tracker in Notion, and save a config page so the weekly check (`/ndis-tracker:ndis-weekly`) can find everything on its own afterwards. Ask one question at a time, in plain, friendly Australian English. Accuracy matters more than speed.

Run this skill once per participant. A family with two participants runs it twice — once for each person.

## Before you start — check connectors

Tell the user, in your own words:

"Before we begin, please make sure you've connected these to Claude:
- **Gmail** — so I can read your NDIS invoices
- **Google Drive** — so I can save invoice PDFs
- **Notion** — where your tracker lives (it has a free plan)

You can connect these at claude.ai/customize/connectors. Type YES when you're ready."

Wait for confirmation. If a connector isn't available when you try to use it, stop and tell the user which one to connect.

## Questions — ask one at a time, in this order

1. **Participant name.** "What is the NDIS participant's full name?"

2. **Gmail label.** "What Gmail label have you set up for this person's NDIS invoices? (For example: 'Sarah NDIS'.) If you haven't made one: open Gmail → left sidebar → 'More' → 'Create new label' → name it like '[Name] NDIS' → Create. What's it called?" Work out a search-safe version too (lowercase, spaces to hyphens) — e.g. "Sarah NDIS" becomes "sarah-ndis".

3. **Google Drive folder.** "Which Google Drive folder should invoice PDFs be saved into? Paste the folder link (right-click the folder → 'Copy link'). If you haven't made one: Drive → New → Folder → name it '[Name] - NDIS' → right-click → Copy link." Extract the folder ID (the long string after `/folders/`).

4. **Funding categories.** Show this privacy note first:

   "A quick note on privacy: your funding details are stored only in your own Notion workspace, in the tracker I'm about to create. They're never sent anywhere else. Claude reads them only during your weekly check. You can edit them any time in Notion."

   Then: "Which NDIS funding categories are active in this plan, and the total budget for each in the current plan period? Common ones: Core Supports; Capacity Building – Improved Daily Living; Capacity Building – Support Coordination; Capital – Assistive Technology. List whichever apply, with the dollar amount for each."

5. **Plan dates.** "What are the start and end dates of the current NDIS plan? (For example: 1 July 2025 to 30 June 2026.)"

6. **Weekly check day.** "What day would you like to run your weekly check? (Skills don't run by themselves — you open Claude and run the check — but I'll note your preferred day so you can set a reminder.)"

## Create the tracker in Notion

Use the participant's full name in EVERY database and page title below, so nothing ever collides with another participant's tracker.

### A. Invoice tracker database

Create a Notion database titled **"NDIS Invoice Tracker — [Participant Name]"** with these properties:

- **Invoice Number** (title)
- **Provider** (text)
- **Service Date** (date)
- **Amount** (number, dollar format)
- **Due Date** (date)
- **Status** (select: Invoice received / Provider paid / NDIS claim submitted / NDIS reimbursed)
- **Funding Category** (select: one option per active funding category)
- **Date Added** (date)
- **Notes** (text)

Note the database's data source ID (the `collection://...` value) — you'll save it in the config page.

### B. Budget tracker database

Create a Notion database titled **"NDIS Budget Tracker — [Participant Name]"** with:

- **Category** (title)
- **Total Budget** (number, dollar format)
- **Spent to Date** (number, dollar format)
- **Remaining** (number, dollar format)
- **Plan Start** (date)
- **Plan End** (date)

Add one row per funding category. Set Total Budget to the amount given, Spent to Date to 0, Remaining to the total, and the plan start/end dates. Note this database's data source ID too.

### C. Config page

Create a Notion page titled **"NDIS Config — [Participant Name]"**. Put these `FIELD: value` lines in the page body, one per line (plain text — never JSON):

```
NDIS CONFIG — do not delete. The weekly check reads this to find your tracker.
PARTICIPANT: [full name]
GMAIL LABEL: [label as typed]
GMAIL LABEL SEARCH: [search-safe version]
DRIVE FOLDER ID: [folder id]
INVOICE TRACKER: collection://[invoice tracker data source id]
BUDGET TRACKER: collection://[budget tracker data source id]
PLAN START: [YYYY-MM-DD]
PLAN END: [YYYY-MM-DD]
WEEKLY CHECK DAY: [day]
```

### D. Confirm

Tell the user plainly:
- Their tracker (two Notion databases) and config page are created — name them.
- From now on they run `/ndis-tracker:ndis-weekly` for their weekly check. They never run setup again for this person.
- To set up another participant, run `/ndis-tracker:ndis-setup` again.
- To change a budget later, edit the Budget Tracker in Notion directly.

Do not generate a separate skill for the user to save. Everything the weekly check needs is in the config page, which it finds on its own.
