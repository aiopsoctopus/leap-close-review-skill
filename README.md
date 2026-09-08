# Month-End Close Review — Claude Skill

A Claude skill built for Leap's take-home case study: automate the mechanical scanning of a month-end close — checking every account, every accrual, every aging item — so a controller's time goes to the judgment calls that actually need a human, not to hunting for them.

## What it does

Scans four things across a close and flags what needs review, with a plain-English reason for every flag:

1. **Unexplained variance** — account balances that moved more than 15% and $2,000 period-over-period, with no clear driver in the transaction detail.
2. **Missing accrual reversals** — accruals booked one period with no matching reversal the next, which can quietly overstate a balance.
3. **Aged receivables/payables** — items outstanding more than 60 days.
4. **Large, undocumented transactions** — anything above $1,500 with no description or memo.

See [`sample-output.md`](./sample-output.md) for a real run against the included mock data, and [`skill/SKILL.md`](./skill/SKILL.md) for the full logic and thresholds.

## What it deliberately does NOT do

It never posts, approves, or auto-resolves anything. Every output is a flag with a reason — the decision stays with a human. That's a design choice, not a limitation: a false positive costs someone two minutes to dismiss; a missed real issue costs a lot more. The tool is built to over-flag slightly rather than under-flag.

## How it connects to real data

This was built and demoed against realistic mock data shaped like real QuickBooks exports (trial balance, GL detail, AR aging — see [`mock-data/`](./mock-data)), so the logic could be tested and shown without touching production financials.

In production, it's designed to run **fully automated through the QuickBooks Online MCP connector** — no exports, no CSV uploads. The skill queries QuickBooks directly each time it runs, the same reports a controller would pull by hand, except scanned in full rather than sampled. To validate this was real and not theoretical, I registered a developer app in the Intuit Developer Portal, created a QuickBooks Online sandbox company, and worked through Intuit's OAuth flow against the official [`quickbooks-online-mcp-server`](https://github.com/intuit/quickbooks-online-mcp-server).

## Why this approach, not a generic AI feature

QuickBooks' own native AI (Intuit Assist / Intuit Intelligence) is bounded to QuickBooks itself and built to work generically across any small business. This skill is designed to be Leap-specific and to extend beyond QuickBooks as Leap adds systems — banking, payroll, RCM — and it's explicitly rule-based and explainable rather than a black box, in line with the idea that a close review tool should be as transparent as the numbers it's checking.

## What's in this repo

- `skill/SKILL.md` — the skill definition: logic, thresholds, output format, constraints.
- `mock-data/` — sample trial balance, GL detail, and AR aging, shaped like real QuickBooks report exports.
- `sample-output.md` — a real run of the skill against the mock data.
- `automation-ideas.md` — a few additional automation ideas for finance, people ops, and cross-functional work, built around specific needs surfaced in conversation.

---

*Built by Christina Moore for Leap Health's Finance & Business Operations Lead case study.*
