---
name: estimate-reimbursement
description: Estimate the Medicare allowable for a DME, orthotic or prosthetic HCPCS code (rental against purchase, new against used, rural against non-rural, capped rental and competitive bidding) nationally or for one state. Use when the user asks what Medicare pays for an item, compares renting and buying, or prices an item for a state.
---

# Estimate reimbursement

Give the Medicare allowable for an item and explain which rate applies.

## Patient privacy (PHI-free)

Send the code, the state and a modifier only. Never put a patient name, date of birth, Medicare
Beneficiary Identifier (MBI), member or record number, address, ZIP code or any other patient identifier
in a tool call. Whether the beneficiary lives in a rural area is a yes or no question; never ask for the
ZIP code.

## 1. Collect

- The HCPCS code.
- The state, when the user wants exact rates.
- Whether the user is weighing rental, purchase, or both.

## 2. The national picture

Call `reimbursement_basic` (free) with the code. For a DMEPOS item it returns the national ranges from the
current CMS DMEPOS fee schedule quarter (rental and purchase, minimum to maximum across states). For an
item in the current round of competitive bidding it adds a competitiveBidding block saying when the
single payment amount replaces the fee schedule amount. For a CPT professional service it returns the
national physician fee schedule payment; stop there, because the state lookup has no CPT rates.

## 3. State rates

When the user wants a state's exact rates: `fee_schedule_lookup` costs 10 credits ($0.01), paid from
prepaid credits (1 credit = $0.001). Unless the balance is already known in this conversation, call
`account_status` (free) first, and tell the user the price before the call. When the balance is short, call
`buy_credits` (free) and hand over the checkout link: it charges nothing until the user pays in the
browser.

Call `fee_schedule_lookup` with the code and the state, and with RR, NU or UE when the user asked about
rental, new purchase or used purchase only.

## 4. Explain

- The monthly rental (RR) against the purchase price, new (NU) or used (UE).
- Non-rural against rural: the rural rate applies when the beneficiary lives in a rural area.
- Capped rental: when the item is rented, call `modifier_advisor` (free) with the code for the
  capped-rental month modifiers (KH, KI, KJ) and how the rental period runs. The RR amount is the rate for
  rental months 1 to 3; from month 4 Medicare pays 75% of it (42 CFR 414.229(b)(2)). A total over the
  13 months is three months at the RR amount plus ten at 75% of it, never the RR amount times 13. Power
  wheelchairs follow a different split (414.229(b)(3)): give their monthly rate without totaling it.
- Competitive bidding: once the round's contracts start, the single payment amount replaces the fee
  schedule amount for the item.

Close by saying these are Medicare allowables before the deductible and coinsurance, not a guaranteed
payment, and that other payers set their own rates.
