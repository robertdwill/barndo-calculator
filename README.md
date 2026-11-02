# VisionBuilt Barndominium Budget Planner

Single-file planner (`index.html`, Tailwind via CDN). Open it in a browser, no build step.

## Connect it (edit the CONFIG block at the top of the script)
- `STRIPE_PAYMENT_LINK` - your Stripe Payment Link for the $9 unlock.
- `GHL_WEBHOOK_URL` - the GoHighLevel inbound webhook that receives the lead payload.
- `UNLOCK_HASHES` - SHA-256 hashes of valid unlock codes (uppercase, trimmed). Make one:
  `python -c "import hashlib,sys;print(hashlib.sha256(sys.argv[1].strip().upper().encode()).hexdigest())" CODE-HERE`

## Honest limits
The unlock is client-side, so a determined visitor can bypass it. Use long random codes and
treat the $9 as an honor-system purchase. Estimates are planning ranges, not quotes.
