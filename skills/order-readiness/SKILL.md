---
name: order-readiness
description: Check what a DMEPOS order needs before delivery for a HCPCS code (written order elements, face-to-face and written-order-prior-to-delivery rules, prior authorization, competitive bidding) and optionally audit the order's documentation pass or fail. Use when the user is about to deliver or bill an item, asks what paperwork an item needs, asks whether a code needs a face-to-face visit or prior authorization, or wants an order checked before delivery.
---

# Order readiness

Answer "what does this item need before it ships" for Original Medicare, then, when the user has the order
in hand, check it requirement by requirement.

## Patient privacy (PHI-free)

Send the code, the date of service, the state and yes or no answers only. The audit asks whether the
beneficiary's name is on the written order, never for the name itself. Never put a patient name, date of
birth, Medicare Beneficiary Identifier (MBI), member or record number, address or any other patient
identifier in a tool call.

## 1. Collect

- The HCPCS code.
- The date of service: today by default, or the planned delivery date. List changes take effect on fixed
  dates, so a delivery next month is checked against next month's lists.
- The state the item is delivered in. Some prior-authorization items are phased in by state.

## 2. The checklist

Call `order_readiness_checklist` (free) with the code, the date of service and the state, and present a
blank pre-delivery checklist:

- The standard written order (SWO) elements.
- Whether the item is on the face-to-face and written-order-prior-to-delivery list (F2F/WOPD), and if so
  the face-to-face encounter in the six months before the order and the written order received by the
  supplier before delivery.
- Whether prior authorization must be affirmed before delivery, with the state phase-in. When the reply
  has a probationaryPa block, add that the separate probationary list applies only to newly enrolled
  suppliers.
- For a replacement item, whether a new face-to-face encounter is needed (the replacementItem block).
- For an item in the current round of competitive bidding, the product category and the dates from the
  competitiveBidding block.

When the question turns to rental or purchase, KX or capped-rental months, call `modifier_advisor` (free)
with the code.

## 3. Audit the order (optional)

Offer the audit when the user has the order in hand. `compliance_audit` costs 250 credits ($0.25), paid
from prepaid credits (1 credit = $0.001). Unless the balance is already known in this conversation, call
`account_status` (free) first, and tell the user the price before the call. When the balance is short, call
`buy_credits` (free) and hand over the checkout link: it charges nothing until the user pays in the
browser.

Ask for every fact in one message, each as yes or no:

- On the written order: the beneficiary's name, the item description, the quantity, the order date, the
  practitioner's name, the practitioner's signature.
- A face-to-face encounter in the six months before the order.
- Whether the item replaces one the beneficiary already has.
- Whether the written order reached the supplier before delivery, and whether it reached the supplier
  before the claim is submitted. The rule turns on when the supplier received the order, not the date
  written on it.
- The prior-authorization status: affirmed, non-affirmed, pending, not requested or exempt.

A fact left unanswered counts as a fail, so say which facts are missing before the call and ask whether to
go ahead. Call `compliance_audit` once with the code, the date of service, the state and the answers.
Report each requirement as pass or fail with its citation, then what to fix before delivery. A code the
tool refuses, for example one billed to the A/B MAC rather than a DME MAC, is refunded.

## Limits

The audit checks the facts the user reports, not the documents. It does not check LCD coverage criteria or
medical-necessity documentation, proof of delivery or billing modifiers, and it is not a coverage decision.
