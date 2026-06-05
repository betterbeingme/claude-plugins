# Better Being Me — Claude plugins

Plugins for Claude, by [Better Being Me](https://betterbeingme.com.au).

## NDIS Tracker

A self-managed NDIS invoice and budget tracker. Set it up once per participant, then run a weekly check that files invoices from Gmail to Google Drive, updates a tracker in Notion, and tells you what to pay and what to claim. It never moves money — paying providers and lodging NDIS claims stay your decision.

### What you need

- A Claude account with the desktop app
- Gmail, Google Drive, and Notion connected (claude.ai/customize/connectors)

### Install

1. In Claude, open **Customize → Plugins**
2. Click **Add marketplace** and enter: `betterbeingme/claude-plugins`
3. Find **ndis-tracker** and click **Install**

### Use

- Run **`/ndis-tracker:ndis-setup`** once for each participant to create their tracker.
- Run **`/ndis-tracker:ndis-weekly`** each week to check invoices and budget.

---

*Free to use. Built from a tool Joanne uses for her own family.*
