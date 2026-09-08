---
name: month-end-close-review
description: Review a month-end close for a healthcare services company by scanning trial balance, GL detail, and AR/AP aging data, flagging items that need human judgment — unexplained variances, missing accrual reversals, aged receivables/payables, and undocumented large transactions. Use when asked to review, audit, or check a monthly close, or to find issues before books are finalized.
---

# Month-End Close Review Skill

## Purpose

Compress the mechanical scanning work of a month-end close — checking every account, every accrual, every aging bucket — so a controller's attention goes to the small number of items that actually need human judgment. This skill never posts, approves, resolves, or changes anything in the books. It only flags and explains.

## Data sources

In production, this skill connects live via the **QuickBooks Online MCP connector** — no exports, no CSV uploads, no manual pulls. It queries the trial balance, GL detail, and AR/AP aging reports directly from QuickBooks each time it runs, the same reports a controller would pull by hand, except scanned in full rather than sampled.

For this exercise, it reads from local mock data files shaped like real QuickBooks report exports:
- `mock-data/trial-balance.csv` — account, type, prior period balance, current period balance
- `mock-data/gl-detail.csv` — date, account, description, memo, amount, transaction type
- `mock-data/ar-aging.csv` — customer, invoice date, amount, days outstanding, status

## What to check

Run all four checks below. For each, only flag items that clear the stated threshold — the goal is a short, high-signal list, not exhaustive noise.

### 1. Unexplained variance (account-level)
Compare prior period balance to current period balance for every account in the trial balance.
- **Flag if:** the change exceeds **15% AND $2,000 absolute** (both conditions, so small accounts don't trigger on tiny percentage swings, and large accounts don't hide meaningful dollar moves under a high percentage bar).
- Check the GL detail for a transaction that plausibly explains the move (e.g., a large bill in that account this period). If a plausible explanation exists, note it but still flag for review at a lower priority. If no plausible explanation is found, flag at high priority.
- **Explanation template:** "[Account] moved from $[prior] to $[current], a [X]% change ([direction]). [Plausible driver found: reference the transaction — OR — No clear driver found in GL detail.] Recommend review."

### 2. Missing accrual reversal
Standard accrual accounting practice: an accrual booked in one period should be reversed in the next period (then re-accrued if still owed). Scan the GL detail for accrual-type journal entries and confirm each has a matching reversing entry in the following period.
- **Flag if:** an accrual entry has no matching reversal in the subsequent period, and the trial balance shows the same or similar balance persisting (suggesting it's sitting unreversed rather than being a fresh new accrual).
- **Explanation template:** "[Account] accrual of $[amount] booked [date] does not appear to have a reversing entry in the following period. This may overstate the balance — recommend confirming whether this should reverse or if it reflects an ongoing liability."

### 3. Aged receivables/payables
Scan the AR (and AP, if provided) aging data.
- **Flag if:** an item is outstanding more than **60 days**.
- **Explanation template:** "[Customer/Vendor] — $[amount] outstanding [X] days as of close. Recommend confirming whether this is a collection issue, a billing/TPA processing delay, or a data entry error."

### 4. Large transaction, thin documentation
Scan GL detail for transactions above a materiality threshold with no memo/description, or coded to a generic account.
- **Flag if:** amount exceeds **$1,500** AND both the description and memo fields are blank or generic.
- **Explanation template:** "[Account] — $[amount] on [date] has no description or memo. Given the size, recommend confirming classification and business purpose before close."

## Output format

Always return a single prioritized list — not a raw dump of every check run separately. Rank by materiality and risk (unexplained items above documented ones, larger dollar amounts above smaller ones within the same tier).

```
CLOSE REVIEW — [Period]
Data source: [QuickBooks Online, via MCP connector — live / Mock data for this exercise]

🔴 HIGH PRIORITY — no clear explanation found
1. [Flag type] — [Account/item] — [plain-English reason]
2. ...

🟡 REVIEW RECOMMENDED — plausible explanation, confirm before closing
1. ...

✅ NO ISSUES FOUND
[X] accounts reviewed · [X] GL transactions scanned · [X] aging items checked

Nothing above has been changed, approved, or resolved automatically. Every item needs a human decision.
```

## Hard constraints — do not violate

- **Never propose or make a posting, journal entry, or correction directly.** This skill flags and explains only. Any drafted journal entry (a possible future extension) must be clearly labeled as a draft for human review, never auto-posted.
- **Every flag must include a plain-English reason**, not just "flagged" — the reader should understand *why* without opening the underlying data.
- **Don't flag everything to look thorough.** If a period is clean, say so plainly in the "NO ISSUES FOUND" section rather than manufacturing marginal flags. Precision matters more than volume.
- **State the data source explicitly** in every output (live QuickBooks via MCP, or mock data) — never let it be ambiguous whether this ran against real or sample data.
