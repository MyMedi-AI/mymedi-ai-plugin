# MyMedi-AI

![MyMedi-AI](assets/mymedi-ai-logo.png)

DME billing checks from a conversation with Claude. This plugin pairs the MyMedi-AI connector with five
skills for the everyday work of a durable medical equipment (DME) supplier or medical biller, built on
public CMS data.

## What it does

- **Scrub a claim.** Codes, modifiers and the date of service checked before the claim goes out, with the
  prior-authorization and written-order lists for each item, as a fix list in priority order.
- **Work a denial.** A remittance code (CARC) decoded into its likely cause and the next step: correct and
  resubmit, bill the patient or the secondary payer, or appeal, with a redetermination outline.
- **Order readiness.** The paperwork a HCPCS code needs before delivery, and an optional pass-or-fail audit
  of an order's documentation, each requirement with its citation.
- **Estimate reimbursement.** The Medicare allowable for an item: rental against purchase, rural against
  non-rural, capped rental and competitive bidding.
- **Catalog exposure.** A product catalog mapped against the CMS prior-authorization and face-to-face
  lists, with the documentation each item needs on file.

## Use it

1. Install the plugin and connect the MyMedi-AI connector on the plugin's Connectors tab. Sign in with a
   MyMedi-AI account, or create one at [mymedi-ai.com](https://mymedi-ai.com).
2. Ask in plain words:
   - "Check this claim before it goes out: E0601 with RR, A7030, diagnosis G47.33, date of service
     2026-11-02, Texas."
   - "This claim came back CO-50. What now?"
   - "What paperwork does K0823 need before delivery in Ohio?"
   - "What does Medicare pay for E0601 in Florida, rent or buy?"
   - "Which of the codes in this spreadsheet need prior authorization?"

## Patient privacy

The skills send codes, modifiers, dates of service and states, never a patient name, date of birth,
Medicare Beneficiary Identifier or any other patient identifier. When a pasted claim or remittance carries
identifiers, Claude takes the codes and dates and leaves the rest out. MyMedi-AI is PHI-free: a paid call
that carries a recognizable patient identifier is refused before any charge.

## Data

The plugin contains instructions only. It runs no code and stores nothing. Its skills call the MyMedi-AI
connector at `https://mymedi-ai.com/mcp-stream` after sign-in, and every tool they use answers from
MyMedi-AI's own reference data, built from public CMS files. Nothing is sent anywhere else. Buying credits
opens Stripe Checkout in the browser.

## Pricing

The reference lookups the skills start with are free. The claim check, the order audit, state fee schedule
rates and the catalog report are paid from prepaid credits (1 credit = $0.001; packs start at $1), and the
first connector sign-in comes with free starter credits. Claude states the price of a paid step and checks
the balance before it runs it. Nothing is charged inside a conversation: buying credits returns a Stripe
checkout link to complete in the browser.

## Scope

Original Medicare fee-for-service rules and public CMS data. These are references, not determinations: no
skill diagnoses a condition, recommends a treatment, or decides coverage, prior authorization or medical
necessity.

## Support

- Help: [mymedi-ai.com/support](https://mymedi-ai.com/support) · info@mymedi-ai.com
- Privacy: [mymedi-ai.com/privacy](https://mymedi-ai.com/privacy)
- Terms: [mymedi-ai.com/terms](https://mymedi-ai.com/terms)
