---
name: catalog-pa-exposure
description: Map a supplier's HCPCS catalog against the CMS Required Prior Authorization List and the face-to-face and written-order-prior-to-delivery list, with the documentation gates and denial-risk flags for each code. Use when the user shares a product catalog, an item list or a spreadsheet of HCPCS codes, asks which of their items need prior authorization, or prepares for a change to the CMS lists.
---

# Catalog prior-authorization exposure

Show a supplier which of its items need prior authorization or a face-to-face encounter and written order
before delivery, and what each one needs on file.

## Patient privacy (PHI-free)

Send HCPCS codes, a date and a state only. A catalog holds no patient data; when the file is a billing
export with patient columns, read only the code column and never put a patient name, date of birth,
Medicare Beneficiary Identifier (MBI), member or record number, address or any other patient identifier in
a tool call.

## 1. Collect

- The codes, from a pasted list, a spreadsheet or a product export. Keep HCPCS Level II codes only (one
  letter and four digits), drop duplicates, and say how many entries were dropped. The report bills an
  unknown or non-HCPCS code like any other, so clean the list before running it.
- The date to check: today by default, or the date a list change takes effect.
- The states the supplier delivers in. Some items are phased in by state; without a state the report marks
  them as required in some states.

## 2. Price the run and get a yes

`pa_exposure_report` costs 10 credits ($0.01) per code, up to 100 codes a call, paid from prepaid credits
(1 credit = $0.001). Tell the user the total first: 240 codes cost 2,400 credits, $2.40. Call
`account_status` (free) for the balance. When it is short, call `buy_credits` (free) and hand over the
checkout link: it charges nothing until the user pays in the browser.

Ways to spend less:

- **Five codes or fewer:** call `order_readiness_checklist` (free) for each code instead. It answers both
  lists without the catalog summary.
- **Several states:** each state is a separate run. Run the whole catalog once without a state, then run
  only the codes marked as required in some states, once per state.

## 3. Run

Call `pa_exposure_report` with up to 100 codes per call, the date and the state.

## 4. Report

- **Summary:** codes checked, how many need prior authorization, how many are on the F2F/WOPD list, and how
  many are unknown.
- **Table** of every code that needs something: prior authorization and the date it applies from, the
  F2F/WOPD requirement, the documentation gates (standard written order, UTN before the claim, F2F/WOPD
  sequencing) and the denial-risk flags.
- **Unknown codes**, listed separately so the user can correct them.

Offer the table as CSV for the user to save.

## Limits

These are the Original Medicare fee-for-service lists; commercial and Medicare Advantage plans keep their
own prior-authorization lists. The report states list membership and documentation rules, not coverage.
