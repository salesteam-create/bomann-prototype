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

Read `index.html` fully first. Then make ALL of the following targeted changes in order. Do not skip any.

---

### CHANGE 1 — LP1: Update hero copy

**Find the LP1 hero section. Make two edits:**

1. Replace the `hero-label` text from `CONFIGURATOR` → `GET INSPIRED`

2. Replace the hero `<h1>` with:
```
Thousands of completed projects. One recommendation built around yours.
```

3. Replace the hero `<p>` subtext with:
```
Tell us about your space and we'll match you with the right solution — our consultants have seen it all.
```

---

### CHANGE 2 — LP1: Fix door style options (Q1)

Remove the Glass tile. Replace with Bi-fold.

**HTML — find the Glass option tile in Q1 and replace with:**
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

**Also update the Hinged tile image URL to:**
```
https://www.bomann.no/wp-content/uploads/2025/09/foldedor-hvit-1-1.jpeg
```

---

### CHANGE 3 — LP1: Fix interior fittings images (Q3)

Q3 currently shows door/room images. Replace with actual interior shots.

Find the Q3 options grid. Update only the `background-image` URLs inside each `.option-tile-bg` div:

- Basic Shelving → `https://www.bomann.no/wp-content/uploads/2025/09/innredning-white-3.jpg`
- Hanging + Shelves → `https://www.bomann.no/wp-content/uploads/2025/09/Innredning-9.jpg`
- Full Fitted → `https://www.bomann.no/wp-content/uploads/2025/09/innredrning-eik.jpg`

---

### CHANGE 4 — LP1: Add Q5 — Budget

Add a fifth question after Q4 (Size). The question flow becomes 5 steps total. Update the progress bar logic so it reads "STEP X OF 5".

**Q5 — Budget**

The budget options shown depend on what the user selected in Q4 (size). Budget tiles do NOT have background images — use solid navy-toned backgrounds with the price range as large text. Keep them shorter than the image tiles (120px height).

Budget option floors by size:

| If Q4 answer | Budget options to show |
|---|---|
| small | kr 10–20k / kr 20–35k / kr 35–55k / kr 55k+ |
| medium | kr 20–35k / kr 35–55k / kr 55–80k / kr 80k+ |
| large | kr 35–55k / kr 55–80k / kr 80–120k / kr 120k+ |

**Implementation:** Q5 HTML should contain all 12 budget tiles (4 per size group), but only show the 4 relevant ones based on `answers.size`. Add a JS function `updateBudgetOptions()` that hides/shows the correct group when Q5 becomes active. Call it inside `showQuestion(5)`.

Budget tile data structure in JS — add a `budgets` object:
```js
const budgets = {
  small:  [
    { value: 'b1', label: 'kr 10 000 – 20 000', bg: '#3D5268' },
    { value: 'b2', label: 'kr 20 000 – 35 000', bg: '#2D3F54' },
    { value: 'b3', label: 'kr 35 000 – 55 000', bg: '#243448' },
    { value: 'b4', label: 'kr 55 000+',          bg: '#1E2D3D' }
  ],
  medium: [
    { value: 'b2', label: 'kr 20 000 – 35 000', bg: '#3D5268' },
    { value: 'b3', label: 'kr 35 000 – 55 000', bg: '#2D3F54' },
    { value: 'b4', label: 'kr 55 000 – 80 000', bg: '#243448' },
    { value: 'b5', label: 'kr 80 000+',          bg: '#1E2D3D' }
  ],
  large:  [
    { value: 'b3', label: 'kr 35 000 – 55 000', bg: '#3D5268' },
    { value: 'b4', label: 'kr 55 000 – 80 000', bg: '#2D3F54' },
    { value: 'b5', label: 'kr 80 000 – 120 000',bg: '#243448' },
    { value: 'b6', label: 'kr 120 000+',         bg: '#1E2D3D' }
  ]
};
```

Render Q5 tiles dynamically using `updateBudgetOptions()` — clear the Q5 tile grid and inject the correct 4 tiles based on `answers.size`. Each tile uses the `label` as large centred white text (1rem, font-weight 600) and the `bg` as the solid background color. No image needed.

**Budget influences scoring:** Add `budget` as a scoring axis in each package. Map budget values to scores as follows:

| Package | Best budget fit |
|---|---|
| Essentials (12–18k) | b1 scores 3, b2 scores 2, b3 scores 1, rest 0 |
| Classic (22–32k) | b2 scores 3, b3 scores 2, b4 scores 1, rest 0 |
| Nordic (18–28k) | b2 scores 3, b3 scores 2, b1 scores 1, rest 0 |
| Signature (38–55k) | b3 scores 3, b4 scores 3, b5 scores 2, rest 0 |

Add `budget: { b1:N, b2:N, b3:N, b4:N, b5:N, b6:N }` to each package's scores object.

**Update `getQuestionKey()`** to return `'budget'` for step 5.

---

### CHANGE 5 — LP1: Update result card CTA copy and add consultant note

**Find the result card CTA button** in `showResults()`. Change the button label from:
```
Lock This Price →
```
to:
```
Get My Recommendation →
```

**Below the results grid** (above the "Start Over" link), add a consultant note:
```html
<div class="consultant-note">
  <span>&#10003;</span>
  A Bomann consultant will reach out to confirm measurements and fine-tune your recommendation.
</div>
```

Style `.consultant-note`: centered, font-size 0.85rem, color `var(--grey-600)`, padding 20px, with a green checkmark span colored `#3A7D5E`. Max-width 560px, margin 0 auto 16px.

**Also update `lockThisPrice()`** — rename it to `getRecommendation()` and update all call sites. The function still switches to LP2 tab, but also scrolls to top.

---

### CHANGE 6 — LP1: Update packages array

Remove `glass_studio` package. Replace with `Nordic`:
```js
{
  id: 'nordic',
  name: 'Nordic',
  tagline: 'Simple lines. Lasting quality.',
  description: 'A refined bi-fold solution for spaces that need full access without compromise. Clean, precise, and built to last.',
  price_min: 18000, price_max: 28000,
  image: 'https://www.bomann.no/wp-content/uploads/2025/09/foldedor-svart-1.jpeg',
  scores: {
    door_style: { sliding:1, hinged:2, bifold:3 },
    finish:     { white_matte:3, oak_wood:2, dark_grey:2, mirror:1 },
    fittings:   { basic:2, hanging:3, full:2 },
    size:       { small:3, medium:3, large:1 },
    budget:     { b1:1, b2:3, b3:2, b4:0, b5:0, b6:0 }
  }
}
```

In all other packages, rename `glass:` → `bifold:` under `door_style` scores, and add the `budget` scoring axis as specified in Change 4.

---

### CHANGE 7 — Full LP2 rework

LP2 is a **seasonal offer/campaign landing page**. It is NOT product-specific in the template sense — the demo uses summer wardrobe pricing, but the format can be reused for any campaign. It does NOT ask configuration questions. It presents 3 pre-defined wardrobe deals, a square footage input to help the customer find the right fit, and a simple "Claim Offer" flow.

#### 7a — LP2 Hero

Replace existing LP2 hero with:
- Background image: `https://www.bomann.no/wp-content/uploads/2018/09/skyvedor-ai-20.jpg`
- Gold label: `SUMMER PRICING`
- Headline: `Summer Pricing Is Here`
- Subtext: `Lock in this season's best prices on custom Bomann wardrobes. Three configurations available — choose yours and a consultant will handle the rest.`
- No CTA button in hero

#### 7b — LP2 SEO copy block

White background section, centered text, max-width 720px, padding 56px 24px. Add below hero:

```
Bomann's custom-made wardrobes are produced in Norway with millimetre precision — 
tailored to your exact room dimensions and interior needs. This summer, we're offering 
three of our most popular configurations at 50% off the standard price. Each solution 
is bespoke, Norwegian-made, and fulfilled through our nationwide dealer network. 
Enter your wall width below to find your best fit, then secure your price with a 
20% deposit today.
```

Font-size 0.95rem, color `var(--grey-600)`, line-height 1.8. Thin `1px solid var(--accent)` bottom border as divider.

#### 7c — Square footage input

Directly below the SEO copy block, add a width input section (white bg, centered, padding 32px 24px):

```html
<div class="sqft-section">
  <p class="sqft-label">Enter your wall width to find the right fit</p>
  <div class="sqft-input-wrap">
    <input type="number" id="wall-width" placeholder="e.g. 180" min="80" max="400">
    <span class="sqft-unit">cm</span>
    <button class="btn-navy" onclick="filterOfferCards()">Find My Options</button>
  </div>
  <p class="sqft-hint">Don't know your exact width? Browse all options below.</p>
</div>
```

Style `.sqft-section`: centered, max-width 560px, margin 0 auto. `.sqft-input-wrap`: flex row, gap 10px, align center. Input: 140px wide, padding 12px 16px, border 1.5px solid `var(--grey-200)`, border-radius 3px, font-family Poppins. `.sqft-unit`: font-size 0.9rem, color `var(--grey-400)`. `.sqft-label`: font-size 0.95rem, font-weight 500, color `var(--navy)`, margin-bottom 14px. `.sqft-hint`: font-size 0.8rem, color `var(--grey-400)`, margin-top 10px.

**`filterOfferCards()` logic:**
- Read value from `#wall-width`
- If empty or invalid: show all 3 cards normally (no highlight)
- If width < 160: highlight `mirror_essential` card (add class `offer-card--recommended`), dim others slightly (opacity 0.7)
- If width 160–220: highlight `mirror_classic`
- If width > 220: highlight `mirror_signature`
- Scroll smoothly to the offer cards section

`.offer-card--recommended`: add gold `2px` border + a "Best Fit for Your Space" badge (gold bg, white text, small, top of card image). Other cards get `opacity: 0.75` when a recommendation is active.

#### 7d — Offer cards section

Section header:
- Gold label: `AVAILABLE THIS SUMMER`
- Headline: `Choose Your Configuration`
- Subtext: `All configurations feature mirror sliding doors. Prices include fitting guidance and dealer support.`

Scarcity line below header:
```html
<div class="scarcity-line">
  <span class="scarcity-dot"></span>
  3 of 12 slots remaining this month
</div>
```
Style: centered, font-size 0.82rem, color `var(--grey-600)`. Dot: 8px circle, `background: #C0392B`, `border-radius: 50%`, display inline-block, margin-right 8px, vertical-align middle.

**3 offer cards** in a 3-column grid (stack to 1 col on mobile):

```js
const offerPackages = [
  {
    id: 'mirror_essential',
    name: 'Mirror Essential',
    interior: 'Basic shelving with hanging rail',
    size: 'Best for walls up to 160 cm',
    price_original: 24000,
    price_offer: 12000,
    deposit: 2400,
    image: 'https://www.bomann.no/wp-content/uploads/2025/09/innredning-white-3.jpg'
  },
  {
    id: 'mirror_classic',
    name: 'Mirror Classic',
    interior: 'Full-length hanging with shelf modules',
    size: 'Best for walls 160–220 cm',
    price_original: 36000,
    price_offer: 18000,
    deposit: 3600,
    image: 'https://www.bomann.no/wp-content/uploads/2025/09/Innredning-9.jpg'
  },
  {
    id: 'mirror_signature',
    name: 'Mirror Signature',
    interior: 'Fully fitted — drawers, shelves, and hanging',
    size: 'Best for walls over 220 cm',
    price_original: 56000,
    price_offer: 28000,
    deposit: 5600,
    image: 'https://www.bomann.no/wp-content/uploads/2025/09/innredrning-eik.jpg'
  }
];
```

Each card layout:
```
[Interior image — 220px, object-fit cover]
  "Best Fit" badge if recommended (absolute top-left)
[Card body — white, padding 24px]
  Package name — bold, navy, 1.1rem
  Interior description — 0.82rem, grey-600
  Size guidance — 0.8rem, accent gold, margin-bottom 16px
  ──────────────────────────
  STANDARD PRICE  ~~kr XX 000~~  (0.8rem, grey-400, strikethrough)
  OFFER PRICE     kr XX 000      (1.4rem, navy, font-weight 700)
  [50% OFF] badge — gold bg (#C8A97A), white text, 0.7rem, padding 3px 10px, border-radius 2px
  ──────────────────────────
  Trust line: "Norwegian made · Bespoke sizing · Consultant follow-up"
  (0.75rem, grey-400, margin 14px 0)
  ──────────────────────────
  [Claim This Offer] button — full width, accent gold, font-weight 600
```

#### 7e — Offer booking modal

On "Claim This Offer" click, open a modal (reuse existing modal CSS) containing:
- Package name + interior description
- "Offer price: kr XX 000" — large, bold, navy
- "Deposit due today: kr X 000" — smaller, grey
- 3 form fields: Full Name, Email, Phone
- "Confirm & Secure My Price" button (full width, accent)
- Disclaimer: "Simulated payment — no real charge will be made."

On confirm → show confirmation modal:
- Green checkmark icon
- Headline: "Your Price Is Secured"
- Package name + offer price + deposit paid
- Ref ID: `BMN-XXXXX`
- "A Bomann consultant will call you within 1 business day to confirm your measurements and finalise the order."
- "A contract and price confirmation will be sent to your email."
- "Done" button

#### 7f — Remove old LP2 JS, add new

Remove: `renderPackageCards()`, `selectPackage()`, `openPaymentModal()` (old version).

Add: `renderOfferCards()`, `filterOfferCards()`, `bookOffer(id)`, `confirmOffer()`.

Call `renderOfferCards()` on page load.

#### 7g — LP1 → LP2 handoff

Update `getRecommendation()` (formerly `lockThisPrice()`) to switch to LP2 tab and scroll to top. No package pre-selection needed since LP2 has its own offer cards.

---

### CHANGE 8 — Meta description

In `<head>` add:
```html
<meta name="description" content="Custom-made Bomann wardrobes — Norwegian precision, bespoke sizing, delivered through our nationwide dealer network. Summer pricing now available on selected configurations.">
```

---

### After all changes — verify:
1. LP1 hero shows new headline and subtext
2. LP1 Q1 shows Sliding / Hinged / Bi-fold with correct images
3. LP1 Q3 shows actual interior images
4. LP1 Q5 (Budget) appears after Q4, shows correct options based on size selection
5. LP1 results show "Get My Recommendation →" CTA and consultant note below cards
6. LP1 scoring includes budget axis, produces sensible results
7. LP2 hero shows "Summer Pricing Is Here"
8. LP2 sq footage input filters/highlights cards correctly
9. LP2 shows 3 offer cards with strikethrough pricing, "Best Fit" badge when filtered
10. "Claim This Offer" opens modal, confirm shows ref ID and consultant copy
11. LP1 → LP2 tab switch still works
12. Mobile layout intact throughout
