---
name: work-a-denial
description: Decode a DME claim denial from its CARC code, find the likely cause, and decide between correcting and resubmitting, billing the patient or secondary payer, or appealing; outline a Medicare redetermination when an appeal is the route. Use when the user has a denied or reduced DME claim, quotes a remittance code such as CO-16, CO-50 or PR-204, or asks how to appeal a denial.
---

# Work a denial

Turn a denial code into the next action. Every tool in this skill is free.

## Patient privacy (PHI-free)

Send codes and dates of service only. Never put a patient name, date of birth, Medicare Beneficiary
Identifier (MBI), member, claim or record number, address or any other patient identifier in a tool call.
When the user pastes a remittance or a denial letter that carries identifiers, take the codes and dates
from it, leave the rest out, and say that you did.

## 1. Collect

- Each claim adjustment reason code (CARC) with its group prefix (CO, PR, OA or PI), for example CO-50.
- Any remark codes (RARC, such as N115). The tool does not decode them: read them with the user from the
  remittance text.
- The HCPCS code that was denied and the date of service, when the user has them.
- The payer (Original Medicare or another payer) and the date on the denial notice.

## 2. Decode

Call `denial_code_info` (free) for each CARC code. It returns the meaning, the common DME causes, the fixes,
whether the denial is appealable, and related codes.

When a HCPCS code was given, call `order_readiness_checklist` (free) with the code and the date of service.
Documentation and medical-necessity denials usually turn on these requirements: the written order, the
face-to-face encounter, and prior authorization.

## 3. Decide the route

Match the causes against what the user knows about the claim, then recommend one route:

- **Patient or secondary payer:** a PR-group adjustment such as a deductible or coinsurance is not a
  denial to appeal; bill the patient or the secondary payer.
- **Correct the claim:** missing or wrong information, a code or modifier error. Use the fixes the tool
  returns. For Original Medicare, how depends on the remittance: a claim returned as unprocessable (remark
  code MA130) is corrected and sent as a new claim; a processed claim with a clerical error, such as a
  wrong code, modifier, date or place of service, is corrected through a reopening request to the DME MAC.
  Sending that claim again as a new claim gets it denied as a duplicate.
- **Appeal:** the tool says the denial is appealable and the documentation meets the requirement. When a
  required document is missing, say an appeal is unlikely to succeed until it is obtained.

Name the facts that would change the recommendation.

## 4. Outline the appeal

For Original Medicare, the first level is a redetermination by the DME MAC, requested within 120 days of
receiving the initial determination (42 CFR 405.942). For any other payer, use the deadline in that payer's
notice.

Write a reusable outline with placeholders such as [Claim number], [Date of service] and [Beneficiary],
which the user fills in outside the conversation. Never fill a placeholder with patient details.

1. The request: a redetermination of the claim, with the HCPCS code, the date of service and the CARC code.
2. Why the denial is wrong, in one paragraph tied to the requirement the denial turns on.
3. The evidence, mapped requirement by requirement.
4. The attachments: the documents `order_readiness_checklist` names for the item, plus anything the
   denial's causes call for, such as proof of delivery or the prior-authorization decision.

## Limits

The decoding is MyMedi-AI's editorial guidance on CARC codes, not the payer's ruling. The tools see neither
the claim nor the medical record, and they do not predict whether an appeal will succeed.
