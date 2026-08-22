# Vendor Reconciliation Copilot

A transparent, browser-based application that reconciles a vendor statement against an internal ledger, identifies discrepancies, calculates the reconciling balance, and provides source-grounded explanations.

## Live application

https://vendor-reconciliation-copilot.karrivivekgopalreddy.chatgpt.site

## Features

- Upload vendor statement and internal ledger CSV files
- Normalize common column aliases into a shared schema
- Detect exact matches, amount mismatches, date mismatches, and combined mismatches
- Detect possible matches using equal amounts within a configurable date window
- Identify vendor-only and ledger-only transactions
- Calculate vendor balance, ledger balance, and net difference
- Prioritize items requiring human review
- Ask grounded questions about verified reconciliation results
- Filter the review queue and download an audit report
- Process uploaded data locally in the browser

## Tech stack

- Next.js
- React
- TypeScript
- CSS
- Browser File API and Blob API
- Deterministic reconciliation rules
- Rule-based grounded Copilot

## Architecture

```text
CSV inputs
   |
Validation and normalization
   |
Deterministic matching engine
   |
Metrics, confidence, and priority
   |
Dashboard + grounded Copilot + audit report
```

## Matching rules

1. Same ID, date, and amount: Exact Match (100%)
2. Same ID and date, different amount: Amount Mismatch (90%)
3. Same ID and amount, different date: Date Mismatch (85%)
4. Same ID, different amount and date: Amount & Date Mismatch (75%)
5. Different IDs, equal amount, dates within configured window: Possible Match (65%)
6. No corresponding item: Vendor Only or Ledger Only (0%)

Possible matches are never auto-approved and always require manual review.

## Run locally

Requirements: Node.js 20 or later.

```bash
npm install
npm run dev
```

Open http://localhost:3000 and upload the files from `sample_data/`.

For a production build:

```bash
npm run build
npm start
```

## Required CSV schema

```csv
transaction_id,date,description,amount
INV101,2026-08-01,Laptops,50000
```

Accepted aliases include `invoice_no`, `txn_id`, `posting_date`, `narration`, and `total`.

## Assumptions

- Both files use one common currency.
- Transaction IDs are unique within each source.
- ID matches take precedence over possible matches.
- Matching is one-to-one.
- Split payments, credit notes, and many-to-one matching are out of scope.
- Only mock or synthetic data is used.
- Core financial calculations are deterministic.
- The Copilot explains verified results but does not calculate balances or decide matches.

## Design tradeoff

Deterministic rules were selected instead of LLM-based financial matching. This reduces flexibility for complex semantic matching, but makes every result accurate, reproducible, transparent, and auditable. An LLM can later be added only to the explanation layer without changing the reconciliation engine.
