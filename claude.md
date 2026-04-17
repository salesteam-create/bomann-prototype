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