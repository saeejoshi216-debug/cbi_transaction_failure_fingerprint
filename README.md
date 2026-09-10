# Transaction Failure Fingerprint — CBI Innovation Data Challenge 2026

**Why do Irish domestic payments fail?** A data-driven investigation into 2.1M+ Irish domestic transactions, built for the Central Bank of Ireland's Innovation Data Challenge 2026.

🔗 **Live report:** https://cbi-transaction-failure-fingerprint.netlify.app/

## Overview

Ireland's EU Verification of Payee (VoP) mandate went live in October 2025. This report uses 2024 transaction data — predating that mandate, to quantify exactly what VoP would have prevented, and to separate genuine payment infrastructure problems from data quality problems.

**Core finding:** Ireland's payment failure problem is a *data infrastructure problem, not a system capacity problem*. The payment system is functioning correctly, it is correctly rejecting payments to wrong, closed, or blocked accounts.

### Headline numbers

| Metric | Value |
|---|---|
| Transactions analysed | 2,125,237 |
| Confirmed failures | 42,269 (1.989% failure rate) |
| Failures caused by Account Problems | 60.2% |
| Directly preventable by EU VoP (conservative) | 25.1% |
| Total transaction value at risk | €6,570,087 |
| Merchant share of value at risk (9.9% of failures) | 57.1% |

## Key Findings

- **Account Problems dominate.** AC01 (wrong IBAN, 25.1%), AC04 (closed account, 19.9%), and AC06 (blocked account, 15.2%) together cause 60.2% of all failures, the settlement layer is working correctly; the account data feeding it is not.
- **The merchant value inversion.** Merchants account for only 9.9% of failures by count but 57.1% of value at risk (average failed transaction: €898 vs €74 for consumers) — VoP delivers roughly 12× more financial protection per B2B event than per consumer event.
- **One field explains most data-quality failures.** BE04 (missing creditor address) accounts for 88.5% of all Data Quality failures — a single mandatory field check at payment initiation would eliminate most of an entire failure category at zero infrastructure cost.
- **Time-of-day and weekend patterns** were tested rigorously (t-test, Mann-Whitney U, Cohen's d) and reported honestly where statistical significance was mixed, rather than overstated.
- **Synthetic data limitations are surfaced, not hidden.** Where error-code distributions were unrealistically uniform across segments (a synthetic-data artefact), the report flags this explicitly rather than drawing false conclusions.

## Data Architecture

Four datasets were linked and validated end-to-end:

| Dataset | Description | Role |
|---|---|---|
| D01 | Retail payment transactions (12 monthly tables) | Primary source — outcome variable and transaction characteristics |
| D03 | Settlement logs | ISO 20022 error codes — *why* each failure occurred |
| D10 | Consumer + Merchant profiles | Demographics and merchant segment |
| D06 | PSP registry | Market structure — institution type, instant-payment readiness |

Every figure in the report derives directly from a raw database field — no composite scores, no modelled estimates, no weighted indices. Join coverage was validated with SQL `COUNT` checks at each stage.

## Policy Recommendations

The report translates findings into prioritised, evidence-backed recommendations, including:

1. **Monitor and evaluate EU VoP implementation**, prioritising the B2B corridor where value protection is highest.
2. **Mandate the BE04 creditor-address field** at payment initiation (zero infrastructure cost).
3. **Introduce an account-closure notification registry** (modelled on the UK's CASS), since VoP alone cannot catch AC04 closed-account failures.
4. **Standardise a PSP-to-PSP account status API** to address AC06 blocked-account failures.
5. **Investigate the Utilities sector's elevated AC01 rate** (35.9% vs. 25.1% average), suggesting stale direct debit mandate data.

## Tech Stack

- **SQL** — data joining, validation, and cohort analysis across the four linked datasets
- **Statistical testing** — t-tests, Mann-Whitney U, Cohen's d for significance and effect-size checks
- **HTML/CSS/JavaScript** — interactive report
- **[Chart.js](https://www.chartjs.org/)** — data visualisations

## Limitations

The report includes a dedicated limitations section covering synthetic data uniformity, partial D03 settlement coverage (66.2%), the VoP impact range (25–45%), and other caveats — full detail in the [report itself](#).
