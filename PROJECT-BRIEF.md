# VisionBuilt Barndominium Budget Planner: project brief

Written 2026-11-02. Internal working document for the vault. It is not part of the public repo.
Status: built (v2), committed locally, **not published, not pushed**.

## What it is
A single-file web calculator (`index.html`, Tailwind CDN, no backend) that VisionBuilt sells for **$9** as marketing and lead generation. A visitor enters size, shop/living split and finishes and gets a Low / Target / High budget, a cost breakdown, what-if scenarios, a monthly payment view and a printable plan. A lead form posts their plan to GoHighLevel.

## Where things are
| What | Where |
|---|---|
| Working copy (vault) | `H:\My Drive\VisionBuilt\product\barndo-calculator\index.html` |
| Local Git repo (outside Drive, so no `.git` syncs) | `C:\Users\wcgco\dev\barndo-calculator` (2 commits, branch `main`) |
| First version, a quick internal calculator | `H:\My Drive\VisionBuilt\barndo_calculator.html` |
| GitHub account signed in | `robertdwill` |

## Decisions Robert made (2026-11-02)
- Base price is **$175/sf at least**, for fully finished living space. CA, FL and WA run closer to **$200/sf**.
- Numbers are **locked behind the $9 unlock**.
- The GitHub repo will be **public**. Anyone can read the rate table in the source. Robert's choice.
- A state selector, plus a local cost adjustment slider, is wanted.
- Publish to Git when done. Run it past the team (Codex, Gemini, another reviewer). Robert will also ask Gemini himself.

## Pricing logic (Robert's rates)
| Item | Rate |
|---|---|
| Slab | $8.25/sf standard 4"; $11.50/sf heavy 6" shop |
| Steel package | $16.50/sf at 12 ft eave rising to $21.50 at 18 ft, times a pitch multiplier of 1.00 (3:12) to 1.07 (6:12), capped at $23.00 |
| Erection | $8.75/sf at 12 ft eave to $10.25 at 18 ft |
| Insulation | $1.85/sf batt; $3.20/sf spray foam |
| Interior, living | **$138/sf** (see assumptions) |
| Interior, shop | $16.50/sf |
| Interior, dry-in | $28/sf over the whole footprint |
| Porch | slab + steel + erection rates, no insulation or interior |
| Regional | CA, FL, WA +14.3% (about 175 to 200); other states 0%; user slider -15% to +30% |
| Range | Low = Target -10%; High = Target +15% |

Tested results: default 40x60, 14 ft eave, 4:12, all living, standard slab, batt: **$175.94/sf** in TX; **$201.10/sf** with +14.3%. A 60/40 split with a 400 sf porch comes to $320,054 target in TX.

## Assumptions that are mine, not Robert's (need his confirmation)
1. **Living interior = $138/sf.** Robert gave $125/sf. I raised it so the default build reaches the $175/sf base he requires. Alternative: keep $125 and show the gap as a separate line (site work, permits).
2. **Steel and erection scaling.** Robert gave only the endpoints; the straight-line scaling between them and the 1.00 to 1.07 pitch multiplier are my interpolation.
3. **Porch pricing.** No porch rate was given; I applied slab + steel + erection.
4. **+14.3%** is simply 200/175. Other states default to 0% because I found no reliable source.

## Web research (2026-11-02)
- Published ranges are noisy: mostly SEO or lead-gen pages that contradict each other.
- Robert's component rates sit inside published ranges (slab $8 to 12, steel kits $15 to 25, erection $3 to 20 depending on source). Published turnkey ranges run roughly $80 to $180/sf, with outliers higher.
- No source found for CA, FL or WA barndominium costs. One article (Opendoor, conventional houses) lists CA $225, WA $205, FL $170, TX $162. The FL figure is below Robert's ~$200; his figure is used because his word on his own business is the source.
- A paid RSMeans location-factor table would be the proper state-by-state source; not obtained.

## Features in v2
Inputs: width, length, eave, pitch, living/shop split, porch, slab, insulation, interior scope, state, local adjustment, financing (down, rate, years). Quick starts (30x40, 40x60, 50x80). Results: Low/Target/High, $/sf, footprint diagram, cost donut, seven what-if changes, monthly payment, "what this covers" note, shareable link, print/PDF, lead form with consent, spam trap, timeout and duplicate-send lock.

## Still to connect (three constants at the top of the file, all empty)
- `STRIPE_PAYMENT_LINK` (Stripe Payment Link for the $9)
- `GHL_WEBHOOK_URL` (GHL inbound webhook). The custom field keys (`barndo_*`) are placeholders and must match real GHL custom fields.
- `UNLOCK_HASHES` (SHA-256 of each valid code; helper command in the repo README)

## Review log
| Reviewer | Result |
|---|---|
| `check_pricing.py` | Pass on v1. Not re-run on v2. |
| Node math and syntax tests | Pass. Not tested in a real browser. |
| Codex (code and pricing math) | Math matches the stated rates. Paywall is client-side and bypassable: use long random codes and treat as honor system. Edge cases (blank sizes, extreme loan inputs) were fixed in v2. Top conversion idea: show a free personalized estimate and charge for the report. |
| Gemini (product and UX) | Wants a free target price as proof, auto-unlock after Stripe, and trust signals (headshot, location, guarantee). Suggested loft, door count, bath count and site-work checklist (each needs prices; not added). Its mobile history-throttling point was applied (URL write debounced). Its accessibility claim was wrong (charts already labeled). |
| Third reviewer (GPT bridge) | See the section "Third reviewer" below. |
| Formal review record (`review_orchestrator.py`) | **Not run.** Reviews so far are manual consults only. |

## Third reviewer: GPT (via the bridge), 2026-11-02
Saved response: `_shared/gpt-bridge-responses/2026-11-02T180045Z.txt`. Findings, each a claim to confirm, not a fact:
- **$138/sf is reverse-engineered** to hit $175, so the output can look more evidence-based than it is. Same concern as assumption 1 above. It also said Codex and Gemini did not challenge it.
- **Three-state grouping (+14.3%)** is a business assumption, not a state cost factor, and Florida data may differ. The tool should say so if it ever shows the number.
- **Clarifications the model needs written down** (this is how v2 actually works in code):
  - Dry-in ($28/sf, whole footprint) **replaces** the living and shop interior rates; it is not added to them.
  - $/sf shown to the buyer = Target / **enclosed footprint** (living + shop), porch excluded from the denominator but included in the total.
  - The regional adjustment is **one multiplier (1 + pct/100) applied to every line**, porch included. The state sets the slider, the buyer can move it; there is no second compounding step.
  - Low/Target/High are fixed -10% / 0 / +15% of Target. They are a planning band, not a statistical confidence interval.
- **Exclusions must be impossible to miss.** v2 has a "what this estimate covers" note. Candidate for a stronger, more visible placement near the totals.
- **Legal and compliance flags (not legal advice):** text and call consent wording (TCPA, state rules: seller identity, message frequency, opt-out), a privacy policy and data handling for GHL leads, sales-tax treatment of a $9 digital product in Stripe, and abuse of an exposed webhook URL. The v2 consent checkbox says contact by email, call or text but has no seller identity, frequency or STOP wording yet.
- **Public repo:** keep unlock hashes, the webhook URL and the Stripe link out of Git. The public repo should carry empty CONFIG values; the deployed copy carries the real ones.
- **Not end-to-end sellable yet:** Stripe, unlock fulfillment (how a code reaches the buyer), GHL field mapping and production testing remain disconnected.
- **Conversion:** same top suggestion as Codex and Gemini: show a free personalized Target estimate, charge for the itemized range, assumptions, scenarios, printable plan and saved report.

Three independent reviewers (Codex, Gemini, GPT) now agree on the free-teaser change. Robert's ruling is still "locked".

## Open questions for Robert
1. Is $138/sf right for living interior, or should the gap be a separate line?
2. Free target-price teaser (Codex and Gemini both recommend), or fully locked?
3. Headshot, city and phone near the buy button? A money-back guarantee line is only his to make.
4. Prices for loft, shop doors, bathrooms and site work, if wanted.
5. Which other states, if any, are high- or low-cost markets?
6. Texting consent: OK to add seller name, message frequency and STOP wording to the lead form, and who writes the privacy policy?
7. Push to public GitHub now, or after Stripe and GHL are connected? Recommendation: after, so the first public version works end to end.

## Honest limits
- The $9 unlock is client-side. A determined visitor can bypass it, and a public repo shows the rates in plain text.
- The output is a planning range, not a quote. Rates are Robert's; regional and interpolated values are flagged above.
- American Iron and other client data are not used anywhere in this product.
