# Partner Onboarding Flow — Mr.Shop

Mr.Shop has two distinct partner types with different onboarding needs: **brands
(products)** and **professionals (stylists / color analysts)**. Both flows are
designed to be lightweight since Mr.Shop's value depends on partners being able
to join easily.

## 1. Brand / Product Partner Onboarding

**Step 1 — Application**
Brand submits: business name, registration/GST details, contact info, category
(e.g. streetwear, formal, athleisure), price range, and sample product catalog
(CSV or API feed if available).

**Step 2 — Verification**
- Business legitimacy check (registration number, basic KYC).
- Manual spot-check of a sample of submitted products for quality/authenticity.
- Confirm fulfillment capability (can they ship to the marketplaces Mr.Shop
  routes through, e.g. Amazon/Myntra, or do they fulfill directly?).

**Step 3 — Catalog Integration**
- Catalog is normalized into Mr.Shop's schema: `product_id, name, category,
  price, style_tags, size_range, stock_status, image_url`.
- Style tags are either self-declared by the brand (from a fixed taxonomy) or
  auto-tagged using a lightweight classifier on product images/descriptions,
  with the brand able to review/correct tags before going live.

**Step 4 — Go-Live**
- Catalog enters the recommendation pool.
- Brand gets a lightweight dashboard (or just a scheduled report) showing
  impressions vs. conversions once live.

## 2. Professional Onboarding (Stylist / Color Analyst)

**Step 1 — Application**
Name, credentials/portfolio, specialty tags (e.g. color analysis, formal
styling, streetwear styling), consultation price, availability calendar.

**Step 2 — Verification**
- Credential/portfolio review (manual, since quality here is reputational
  risk for Mr.Shop).
- Trial consultation or reference check before full listing.

**Step 3 — Integration**
- Profile added to the partner catalog with the same tag taxonomy used for
  products, so professionals can be matched using the same engine as brands.
- Calendar synced (e.g. via Calendly-style API) for booking availability.

**Step 4 — Go-Live**
- Professional appears in recommendations when a user's profile/style tags
  align, or when a user explicitly asks for a consultation.

## Key Design Principle

Both partner types are normalized into **the same tag taxonomy** (style tags,
price/price-tier, category) specifically so a single matching engine (see
`match.py`) can rank *both* products and professionals side by side, without
needing separate logic per partner type. This keeps onboarding simple to
extend — adding a new partner type later just means mapping it into the same
tag schema.
