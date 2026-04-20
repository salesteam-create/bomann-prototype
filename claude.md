# Bomann Prototype — Claude Code Brief

## What We're Building
A prototype of two interactive landing pages for **bomann.no**, a Norwegian manufacturer of bespoke custom wardrobes and sliding doors. This is a client-facing demo to showcase a new digital sales concept. It does not need a backend — everything is simulated.

Both pages live on a single HTML/CSS/JS site, toggled via two tabs in the header.

---

## Brand

- **Company:** Bomann — premium, precise, Scandinavian. Understated, not flashy.
- **Tone:** Professional, clean, confident. Norwegian quality positioning.
- **Typography:** Poppins (Google Fonts)
- **Colors:** See `tokens.css` — primary is dark navy `#2D3F54`, white backgrounds, subtle greys.
- **Logo:** `/assets/bomann-blue.png`
- **Aesthetic reference:** Existing site at bomann.no — lots of whitespace, dark text, minimal UI, clean photography placeholders.
- **Do NOT:** Use gradients, rounded pill buttons, colorful icons, bright accent colors, or anything that looks like generic SaaS.

---

## Site Structure

```
index.html         ← single self-contained file. All HTML, CSS, and JS lives here.
/assets/           ← logo (bomann-blue.png) and any images
tokens.css         ← CSS variable reference (do not import — values are inlined in index.html)
content.json       ← copy and pricing reference (data is inlined in index.html JS)
```

**Important:** `index.html` is fully self-contained. All styles are in a `<style>` block, all logic is in a `<script>` block at the bottom. There are no separate `style.css`, `lp1.js`, or `lp2.js` files. Do not split the file unless explicitly asked.

### Images
All product images are loaded directly from Bomann's live CDN. Do not use placeholder images:
- Hero (LP1): `https://www.bomann.no/wp-content/uploads/2018/09/skyvedor-ai-21-1-e1764344278699.jpg`
- Hero (LP2): `https://www.bomann.no/wp-content/uploads/2025/09/upscalemedia-transformed.webp`
- Sliding door tile: `https://www.bomann.no/wp-content/uploads/2018/09/skyvedor-ai-21-1-e1764344278699.jpg`
- Hinged door tile: `https://www.bomann.no/wp-content/uploads/2025/09/foldedor-hvit-1.jpeg`
- Glass door tile: `https://www.bomann.no/wp-content/uploads/2025/09/upscalemedia-transformed.webp`
- Oak finish tile: `https://www.bomann.no/wp-content/uploads/2025/09/innredrning-eik.webp`
- Interior fittings: `https://www.bomann.no/wp-content/uploads/2018/09/skyvedor-ai-21-1-864x1000.jpg`

### Header
- Bomann logo text (left) — styled in Poppins, letter-spaced, with ® superscript
- Two tab toggles (right): **"Get Inspired"** | **"Lock My Price"**
- Tabs switch between LP1 and LP2 sections. Only one visible at a time.
- Header is fixed at top, 72px height, white background with bottom border.

---

## LP1 — "Get Inspired" (Configurator)

### Purpose
Customer answers 4 questions about their wardrobe preferences. The system returns 3–4 product recommendations with price ranges in NOK.

### Flow
1. **Hero** — full-bleed background image (sliding door photo from Bomann CDN), dark navy overlay ~60% opacity, small gold uppercase label "CONFIGURATOR", large headline, subtext, "Start Now" CTA button
2. **"How It Works" strip** — white background section, 3 numbered circle steps: "Answer 4 Questions" / "Get Matched" / "Lock Your Price" with short descriptions. This section is hidden once the user clicks "Start Now".
3. **Configurator** — question flow, one question at a time, progress bar showing "STEP X OF 4"
4. **Results** — 3 scored product cards with Bomann product images as card headers, price ranges, CTAs
5. **Trust strip** — dark navy bar with 4 stats from Bomann's site: 14+ years, 16+ dealers, 13,362+ solutions, 39% satisfied

### The 4 Questions
Each question shows visual tile options the user clicks to select. **Tiles have background images or color fills — not plain white cards.** One answer per question. Questions appear one at a time. "Next" button is disabled until an option is selected. A checkmark appears on the selected tile.

**Q1 — Door Style** (3 cols)
- Sliding → background: sliding door CDN image
- Hinged → background: hinged/fold door CDN image
- Glass → background: glass door CDN image

**Q2 — Finish** (4 cols)
- White Matte → background: solid warm white `#F0EDE8`
- Oak Wood → background: oak interior CDN image
- Dark Grey → background: solid dark `#2A2E34`
- Mirror → background: silver gradient

**Q3 — Interior Fittings** (3 cols)
- Basic Shelving → background: oak interior CDN image (top crop)
- Hanging + Shelves → background: interior wardrobe CDN image
- Full Fitted → background: glass door CDN image

**Q4 — Size** (3 cols, shorter tiles)
- Small (100–150 cm) → background: navy-light solid
- Medium (150–200 cm) → background: navy solid
- Large (200–300 cm) → background: navy-dark solid

All tiles: dark gradient overlay so white text is always legible. Selected state: gold border + checkmark badge top-right.

### Rule-Based Logic
Map answer combinations to one of four product packages using a scoring approach. Logic lives inline in `index.html` `<script>`. Each package has a `scores` object keyed by question → answer → score (0–3). Sum scores across all 4 answers, sort descending, show top 3.

| Package | Best match when... | Price Range (NOK) |
|---|---|---|
| Essentials | Basic fittings + Small/Medium + any door | 12,000 – 18,000 |
| Classic | Hinged/Sliding + Oak or White + Hanging+Shelves + Medium | 22,000 – 32,000 |
| Signature | Sliding + Mirror or Dark Grey + Full Fitted + Large | 38,000 – 55,000 |
| Glass Studio | Glass doors + any finish + any fittings | 45,000 – 70,000 |

Always show 3 results: best match first, then the two next-closest packages. Never show just one result.

### Result Cards
Each card shows:
- **Card header image** (180px tall, Bomann product photo as background)
- "Best Match" gold badge on the top result card (positioned over the image)
- Package name
- Tagline (gold accent color)
- Description text
- "PRICE RANGE" label (small uppercase)
- Price range in NOK (e.g. "kr 22 000 – 32 000") — use Norwegian locale formatting with space as thousands separator
- CTA button: "Lock This Price →" → switches to LP2 tab with this package pre-selected

Best match card gets a gold `2px` border. Other cards get standard grey border.

---

## LP2 — "Lock My Price" (Price Lock)

### Purpose
Customer selects or arrives with a pre-selected wardrobe package, sees the price, pays a 20% deposit to lock it in. Creates a lead. Sends a confirmation.

### Flow
1. **Hero** — full-bleed background image (glass door photo), dark navy overlay, gold label "PRICE LOCK", headline "Lock In Your Price Today", subtext
2. **Two-column layout** (left: package selector + price summary / right: form)
3. **Package selector** — 4 clickable cards showing package name, tagline, and price range. Selected state: navy border. If arriving from LP1 via "Lock This Price →", the correct package is pre-selected.
4. **Price summary box** — shows: Price range / 20% deposit / Due today. Updates live when package selection changes. Deposit = `Math.round((price_min + price_max) / 2 * 0.20)`
5. **Customer details form** — Name, Email, Phone (all required)
6. **"Lock My Price" button** — validates form, then opens payment modal
7. **Payment modal** — overlay, shows deposit amount, 3 fake card fields (Card Number, Expiry, CVC), "Confirm Deposit" button, disclaimer "Simulated payment — no real charge will be made."
8. **Confirmation modal** — success icon, "You're Confirmed" headline, package + deposit summary, HubSpot banner with generated ref ID (format: BMN-XXXXX), contract note, "Done" button

### Deposit Logic
- Deposit = 20% of the midpoint of the price range
- Example: Classic (kr 22,000–32,000) → midpoint kr 27,000 → deposit kr 5,400
- Show both the full range and the deposit amount clearly

### Package Options (same 4 as LP1)
Pull from `content.json`

---

## Interactions & Polish

- Tab switching: smooth fade transition between LP1 and LP2
- Question flow: slide or fade between questions
- Progress bar on question flow
- "Lock This Price →" on LP1 result cards pre-selects that package on LP2
- Confirmation popup: overlay modal, not a new page
- All forms: basic HTML5 validation (required fields)
- Mobile responsive: must work on mobile

---

## What This Is NOT

- No real payment processing
- No real HubSpot integration
- No backend or API calls
- No database
- This is a clickable prototype for a client presentation

---

## File Responsibilities

| File | What it does |
|---|---|
| `index.html` | Everything — HTML structure, all CSS in `<style>`, all JS in `<script>`. Self-contained. |
| `tokens.css` | CSS variable reference only. Values are already inlined in index.html. |
| `content.json` | Copy and pricing reference only. Data is already inlined in index.html JS. |

**Do not create separate .css or .js files unless explicitly asked.**

---

## Build Order (for Claude Code extending the existing build)
1. Read the existing `index.html` fully before making any changes
2. Make targeted edits — do not rewrite the whole file unless asked
3. Preserve all existing Bomann CDN image URLs
4. Test that LP1 → LP2 handoff still works after any changes
5. Keep all styles inside the `<style>` block, all logic inside the `<script>` block

---

## Pending Changes — Implement These Now

Read `index.html` fully first. Then make the following targeted changes in order.

---

### CHANGE 1 — LP1: Fix Door Style options

**What to change:** Q1 (door style) currently has 3 options: Sliding, Hinged, Glass. Remove Glass. Replace with Bi-fold.

**HTML — find the Q1 options grid and replace the Glass tile with:**
```html
<div class="option-tile" data-q="door_style" data-v="bifold" onclick="selectOption(this)">
  <div class="option-tile-bg" style="background-image:url('https://www.bomann.no/wp-content/uploads/2025/09/foldedor-svart-1.jpeg')"></div>
  <div class="option-tile-overlay"></div>
  <div class="option-check"></div>
  <div class="option-tile-content">
    <div class="option-tile-name">Bi-fold</div>
    <div class="option-tile-desc">Full access, compact swing</div>
  </div>
</div>
```

**Also update the Hinged tile image** to use the white hinged door (cleaner photo):
```
https://www.bomann.no/wp-content/uploads/2025/09/foldedor-hvit-1-1.jpeg
```

**JS — in the `packages` array**, remove all `glass: N` score entries and replace with `bifold: N` using the same score values glass had. Also remove the `glass_studio` package entirely. Replace it with a new `Nordic` package:
```js
{
  id: 'nordic',
  name: 'Nordic',
  tagline: 'Simple lines. Lasting quality.',
  description: 'A refined bi-fold solution for spaces that need full access without compromise. Clean, precise, and built to last.',
  price_min: 18000, price_max: 28000,
  image: 'https://www.bomann.no/wp-content/uploads/2025/09/foldedor-svart-1.jpeg',
  scores: { door_style:{sliding:1,hinged:2,bifold:3}, finish:{white_matte:3,oak_wood:2,dark_grey:2,mirror:1}, fittings:{basic:2,hanging:3,full:2}, size:{small:3,medium:3,large:1} }
}
```

---

### CHANGE 2 — LP1: Fix Interior Fittings images

**What to change:** Q3 (interior fittings) currently shows irrelevant room/door images. Replace all three tile backgrounds with actual interior shots.

**Find the Q3 options grid. Update the background images as follows:**

- Basic Shelving tile → `https://www.bomann.no/wp-content/uploads/2025/09/innredning-white-3.jpg`
- Hanging + Shelves tile → `https://www.bomann.no/wp-content/uploads/2025/09/Innredning-9.jpg`
- Full Fitted tile → `https://www.bomann.no/wp-content/uploads/2025/09/innredrning-eik.jpg`

Do not change anything else about Q3 — only the `background-image` URLs inside the `.option-tile-bg` divs.

---

### CHANGE 3 — LP1: Update Q1 scoring references

In the `packages` array scores, find every instance of `glass:` under `door_style` and rename it to `bifold:`. Keep the numeric values identical. This ensures the scoring logic doesn't break when a user selects Bi-fold.

---

### CHANGE 4 — Full LP2 rework

LP2 is no longer a generic package selector. It is a **campaign/offer landing page**. The concept: Bomann has a spring offer running on a specific product (for this demo: mirror wardrobes). The page presents 3 pre-defined configurations upfront. The customer picks one, pays a deposit, done. No configuration questions.

**This is a template** — the product and offer can be swapped out. Mirror wardrobes is just the demo content.

#### 4a — LP2 Hero

Replace the existing LP2 hero content with:
- Background image: `https://www.bomann.no/wp-content/uploads/2018/09/skyvedor-ai-20.jpg`
- Gold label: `SPRING OFFER`
- Headline: `Mirror Wardrobes — Now at 50% Off`
- Subtext: `Limited availability. Secure your price with a 20% deposit today.`
- No CTA button in hero — the offer cards below are the CTA

#### 4b — LP2 SEO copy block

Add a new section directly below the hero (white background, centered, max-width 720px, padding 48px). Content:

```
Bomann's custom-made mirror wardrobes are produced in Norway with millimetre precision — 
tailored to your exact room dimensions and fitted with the interior organisation that works 
for your daily life. This spring, we're making three of our most popular configurations 
available at 50% off the standard price. Each wardrobe is bespoke, Norwegian-made, and 
delivered through our nationwide dealer network. Select your configuration below and secure 
your price with a deposit today.
```

Font size 0.95rem, color `var(--grey-600)`, line-height 1.8. Add a thin `1px` gold bottom border below this section as a divider.

#### 4c — Replace LP2 package selector with offer cards

Remove the existing two-column layout (package-cards list + form). Replace the entire `lp2-section` with a new section structured as follows:

**Section header:**
- Small gold label: `AVAILABLE CONFIGURATIONS`
- Headline: `Choose Your Wardrobe`
- Subtext: `All three configurations feature mirror sliding doors. Select the interior that works for your space.`

**Scarcity line** (below header, before cards):
```html
<div class="scarcity-line">
  <span class="scarcity-dot"></span>
  3 of 12 slots remaining this month
</div>
```
Style: small centred text, `var(--grey-600)`, with a small red dot (`8px`, `background:#C0392B`, `border-radius:50%`) before the text.

**3 offer cards** in a 3-column grid (stack to 1 col on mobile). Each card:

```
[Interior image — open wardrobe, 220px tall]
[White card body]
  Package name (bold, navy)
  Interior description (small, grey)
  Size specification
  ─────────────────────
  STANDARD PRICE   kr XX 000  ← strikethrough, grey
  OFFER PRICE      kr XX 000  ← large, navy, bold
  50% OFF badge               ← gold background, white text
  ─────────────────────
  Norwegian made · Bespoke sizing
  Advisor calls within 1 business day
  ─────────────────────
  [Book This Price] button    ← full width, accent gold
```

**The 3 offer configurations (hardcode these in JS as `offerPackages` array):**

```js
const offerPackages = [
  {
    id: 'mirror_essential',
    name: 'Mirror Essential',
    interior: 'Basic shelving — shelves and hanging rail',
    size: '150 cm wide',
    price_original: 24000,
    price_offer: 12000,
    deposit: 2400,
    image: 'https://www.bomann.no/wp-content/uploads/2025/09/innredning-white-3.jpg'
  },
  {
    id: 'mirror_classic',
    name: 'Mirror Classic',
    interior: 'Hanging + shelves — full-length rail with shelf modules',
    size: '200 cm wide',
    price_original: 36000,
    price_offer: 18000,
    deposit: 3600,
    image: 'https://www.bomann.no/wp-content/uploads/2025/09/Innredning-9.jpg'
  },
  {
    id: 'mirror_signature',
    name: 'Mirror Signature',
    interior: 'Full fitted — drawers, shelves, and hanging',
    size: '250 cm wide',
    price_original: 56000,
    price_offer: 28000,
    deposit: 5600,
    image: 'https://www.bomann.no/wp-content/uploads/2025/09/innredrning-eik.jpg'
  }
];
```

**On "Book This Price" click:** open a modal (reuse existing modal styles) with:
- Selected package name
- Interior description
- Offer price + deposit amount (prominent)
- 3 form fields: Name, Email, Phone
- "Confirm & Pay Deposit" button
- Disclaimer: "Simulated payment — no real charge will be made."

On confirm → show confirmation modal with:
- "Price Locked" headline
- Package name + offer price + deposit paid
- Ref ID (format: `BMN-XXXXX`)
- "A Bomann advisor will call you within 1 business day to confirm measurements and finalise your order."
- "A contract will be sent to your email."

#### 4d — Remove old LP2 JS functions that are no longer needed

Remove or disable: `renderPackageCards()`, `selectPackage()`, `openPaymentModal()` (the old version). Replace with new functions: `renderOfferCards()`, `bookOffer(id)`, `confirmOffer()`.

Call `renderOfferCards()` on page load instead of `renderPackageCards()`.

#### 4e — LP1 → LP2 handoff

The "Lock This Price →" button on LP1 result cards currently calls `lockThisPrice(pkgId)` which switches to LP2 and pre-selects a package. Since LP2 no longer has a generic package selector, update `lockThisPrice()` to simply switch to the LP2 tab and scroll to the offer cards. Do not try to pre-select anything — the user will choose their offer configuration on LP2.

---

### CHANGE 5 — Add meta description for SEO

In the `<head>`, add:
```html
<meta name="description" content="Custom-made mirror wardrobes from Bomann — Norway's leading bespoke storage manufacturer. Tailor-made to millimetre precision. Spring offer now available.">
```

---

### After all changes — verify these things work:
1. LP1 Q1 shows Sliding / Hinged / Bi-fold with correct images
2. LP1 Q3 shows interior images (not door/room images)
3. LP1 scoring produces 3 results with no glass references
4. LP2 shows 3 offer cards with correct prices and images
5. "Book This Price" opens modal, confirm shows ref ID
6. LP1 → LP2 tab handoff still works
7. Mobile layout is not broken
