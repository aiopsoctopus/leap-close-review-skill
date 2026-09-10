# Sample Output — Month-End Close Review Skill

*Run against `mock-data/` for demo purposes. This is what the skill produces.*

```
CLOSE REVIEW — August 2026
Data source: Mock data for this exercise (production: QuickBooks Online, live via MCP connector)

🔴 HIGH PRIORITY — no clear explanation found
1. Missing accrual reversal — Accrued Bonus/Incentive, $45,000 booked 7/31/26 — no reversing
   entry found in August. Balance is unchanged period-over-period, consistent with sitting
   unreversed. Recommend confirming whether this should reverse or reflects an ongoing liability.
2. Undocumented transaction — Office & Admin, $4,200 on 8/19/26 — no description or memo.
   Recommend confirming classification and business purpose before close.
3. Aged receivable — United Airlines (Employer Plan), $14,750 outstanding 109 days. Recommend
   confirming whether this is a collection issue, a TPA processing delay, or a data entry error.

🟡 REVIEW RECOMMENDED — plausible explanation, confirm before closing
1. Software & Subscriptions — moved from $18,200 to $41,600 (+128.6%). Explained by a $23,400
   annual platform license renewal booked 8/15. Worth confirming: should a 12-month license be
   recorded as a prepaid asset and amortized monthly, rather than expensed in full this period?
2. Accounts Payable — moved from $196,300 to $241,800 (+23.2%). Several new vendor bills this
   period plausibly explain the increase. Recommend confirming completeness — no obvious
   duplicate or missing entry, but worth a quick tie-out.

✅ NO ISSUES FOUND
Accrued Payroll — booked and reversed correctly this period (no flag; included to show the
check is precise, not indiscriminate). 16 other accounts reviewed, 12 GL transactions scanned,
5 AR items checked — no other issues found.

Nothing above has been changed, approved, or resolved automatically. Every item needs a human decision.
```

