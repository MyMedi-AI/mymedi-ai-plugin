---
name: scrub-claim
description: Check a DME claim's codes, modifiers and date of service before it is submitted, and turn the findings into a fix list in priority order. Use when the user asks to check, scrub or review a claim before sending it, asks whether codes are valid on a date of service, or pastes the codes from a claim.
---

# Scrub a claim before submission

Catch what a payer would deny before the claim goes out: invalid or inactive codes, malformed modifiers, a
date of service in the future, and items that need prior authorization or a written order
before delivery. The list checks are Original Medicare's; for another payer, say so in the answer.

## Patient privacy (PHI-free)

Send codes, modifiers, dates of service and states only. Never put a patient name, date of birth, Medicare
Beneficiary Identifier (MBI), member or record number, address or any other patient identifier in a tool
call. When the user pastes a claim that carries identifiers, take the codes and dates from it, leave the
rest out, and say that you did.

## 1. Collect the claim once

Ask for everything in one message:

- Each claim line: the HCPCS or CPT code and its modifiers.
- The ICD-10-CM diagnosis codes, at least one.
- The date of service (YYYY-MM-DD).
- The place of service code, when the user has it.
- The state the item is delivered in. Some prior-authorization items are phased in by state.

## 2. Run the free list checks

For each HCPCS Level II code (one letter and four digits), call `order_readiness_checklist` (free) with the
code, the date of service and the state. It says whether the item is on the CMS Required Prior
Authorization List, on the face-to-face and written-order-prior-to-delivery list (F2F/WOPD), and in the
current round of competitive bidding.

When a line carries KX, GA, GY, GZ, RR, NU, UE, KH, KI, KJ, RT or LT, call `modifier_advisor` (free) with
the code and a short scenario phrase, such as "ABN on file" or "bilateral", to check that the modifier fits
the item.

## 3. Validate the claim

`claims_validate` costs 50 credits ($0.05), paid from prepaid credits (1 credit = $0.001). Unless the
balance is already known in this conversation, call `account_status` (free) first, and tell the user the
price before the call. When the balance is short, call `buy_credits` (free) and hand over the checkout
link: it charges nothing until the user pays in the browser, and the credits arrive after checkout.

Call `claims_validate` once with the date of service, the diagnosis codes, the procedure codes, the
modifiers and the place of service. It checks that each code exists and is active on the date of service,
that each modifier is well-formed, and that the date of service is not in the future. A modifier its DMEPOS
reference does not list comes back as a warning, not an error.

## 4. Report

Lead with pass or fail, then:

1. **Fix before submitting:** each invalid or inactive code and each malformed modifier, with the fix the
   tool suggests.
2. **Must already be in hand:** for an item on the prior-authorization list, the affirmed decision and its
   unique tracking number (UTN) for the claim; for an item on the F2F/WOPD list, the face-to-face encounter
   in the six months before the order and the written order received by the supplier before delivery.
3. **Warnings:** for example a CPT code missing from the loaded fee schedule file, or a modifier the
   DMEPOS reference does not list. Such a modifier may still be valid: confirm the payer accepts it.
4. A short pre-submission checklist in priority order.

## Limits

Say plainly what the check does not cover: NCCI edits (procedure-to-procedure pairs and MUE unit limits),
medical necessity, coverage and payment. A clean result means the codes and dates are valid on the date of
service, not that the claim will be paid.
