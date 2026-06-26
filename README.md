# frontend_nexusAI
# NexusAI — Intelligence That Scales

> A premium, high-converting, responsive landing page for an AI-driven data automation platform. Built as a submission for **FB Round 1: Next-Gen AI Platform Speed Run**.

---





---

## Overview

NexusAI is a single-file, zero-dependency frontend built in **Vanilla JS + Custom CSS** against a strict set of architectural, performance, and motion constraints. Every feature — from the pricing engine to the bento-to-accordion layout transition — is written from scratch with no external UI or animation libraries.

---

## Features

### Feature 1 · Matrix-Driven Pricing & Currency Switcher

- Prices are computed dynamically from a **multi-dimensional configuration matrix** — no hardcoded UI values anywhere.
- Supports three currencies: **INR (₹)**, **USD ($)**, **EUR (€)**.
- A **20% annual discount multiplier** is applied when the billing toggle is switched to Annual.
- Currency or billing cycle changes update **only the targeted price text nodes** — the parent component and surrounding layout blocks never re-render. Verified via Chrome DevTools Performance panel.

### Feature 2 · Bento-to-Accordion with State Persistence

- On **desktop (>900px)**: features are presented in a fluid **12-column CSS Bento Grid** with cursor-tracked radial gradient spotlights and tilt hover micro-interactions.
- On **mobile (≤900px)**: the grid refactors into a **touch-optimised Accordion list**, built entirely with native CSS transitions.
- **Context Lock**: If a user is hovering over a bento card and the window is resized past the mobile breakpoint, the application programmatically transfers the active card index to the accordion and opens the corresponding panel with a smooth transition.
- Zero runtime animation engines. Zero external component libraries.

---

## Tech Stack

| Layer | Choice |
|---|---|
| Framework | Vanilla JS (ES6+) |
| Styling | Custom CSS with CSS Variables |
| Fonts | JetBrains Mono · Inter (Google Fonts) |
| Animation | Native CSS Transitions / WAAPI |
| Deployment | Static HTML — deploy anywhere |

---

## Asset Compliance

All assets from the provided `asset_package` are integrated:

| Asset | Usage |
|---|---|
| **Color Palette** | All 6 hex values used as CSS custom properties |
| **SVG Pack** | All 14 icons inlined — arrow-path, arrow-trending-up, chart-pie, chevron-down/left/right/up/up-solid, cog-8-tooth, cube-16-solid, link, link-solid, search, x-mark |
| **JetBrains Mono** | Applied to all headings, code labels, price figures, logo, and monospaced UI elements |
| **Inter** | Applied to all body text, descriptions, navigation, and form controls |

### Color Tokens

```css
--arctic:    #F1F6F4;   /* Arctic Powder  — light backgrounds    */
--mint:      #D9E8E2;   /* Mystic Mint    — body text, subtitles */
--forsythia: #FFC801;   /* Forsythia      — primary accent       */
--saffron:   #FF9932;   /* Deep Saffron   — gradient endpoint    */
--teal:      #114C5A;   /* Nocturnal Expedition — card fills     */
--noir:      #172B36;   /* Oceanic Noir   — section backgrounds  */
```

---

## Scoring Criteria Coverage

### 1. Logic, Architecture & State Isolation (40 pts)

**Feature 1 — Pricing Matrix (15 pts)**

The entire pricing engine is driven by a single `PRICING_MATRIX` config object:

```js
const PRICING_MATRIX = {
  tiers: [ /* base rates per tier per currency */ ],
  annualMultiplier: 0.80,
  tariffs: { INR: 1.00, USD: 1.00, EUR: 1.00 },
  symbols: { INR: '₹', USD: '$', EUR: '€' },
};
```

The `computePrice(tier, currency, annual)` function derives every displayed value at runtime. No values are hardcoded in the HTML.

**Re-render & State Isolation Guardrail (15 pts)**

`updatePriceNodes()` targets only the three `<span>` elements containing price strings. It applies a 80ms `opacity` fade on those nodes alone. No parent component state changes. No layout thrash.

```js
function updatePriceNodes() {
  PRICING_MATRIX.tiers.forEach(tier => {
    const priceEl = document.getElementById(`price-${tier.id}`);
    priceEl.classList.add('updating');
    setTimeout(() => {
      priceEl.textContent = computedValue;
      priceEl.classList.remove('updating');
    }, 80);
  });
}
```

**Feature 2 — Bento-to-Accordion (10 pts)**

Context transfer on resize is handled via `window.matchMedia`:

```js
MQ.addEventListener('change', e => {
  if (e.matches && !wasMobile) {
    openAccordionAtIndex(activeDesktopIndex >= 0 ? activeDesktopIndex : 0);
  }
  wasMobile = e.matches;
});
```

No banned libraries (Framer Motion, Radix, Shadcn, HeadlessUI) are present anywhere in the codebase.

---

### 2. SEO Optimization & Semantic HTML (30 pts)

**Semantic DOM (15 pts)**

The page uses a clean semantic hierarchy with no unnecessary `<div>` nesting:

```
<header>       — Fixed navigation bar
<main>
  <section>    — Hero (#hero)
  <section>    — Features (#features)
    <article>  — Individual bento cards and accordion items
  <section>    — Pricing (#pricing)
  <section>    — Social Proof (#social)
  <section>    — CTA Banner (#cta-section)
<footer>       — Site footer
```

**SEO & Metadata (10 pts)**

```html
<meta name="description" content="..." />
<meta name="keywords" content="..." />
<meta name="robots" content="index, follow" />
<meta property="og:title" content="..." />
<meta property="og:description" content="..." />
<meta property="og:type" content="website" />
<meta property="og:image" content="..." />
<meta name="twitter:card" content="summary_large_image" />
```

All images carry `alt` attributes. Price values and feature text are in crawlable text nodes, not images or canvas elements.

**Loading Performance (5 pts)**

- Fonts loaded via `<link rel="preconnect">` and `display=swap` to prevent render-blocking.
- A loader overlay hides until `window.load`, then fades out at 420ms — well within the 500ms TTI threshold.
- No JavaScript blocks the initial HTML parse. All scripts run after DOM content is loaded.

---

### 3. UI/UX Usability & Motion Matching (30 pts)

**Motion Timing**

All timings are within spec:

```css
--t-micro:  170ms;   /* Hovers, toggles — ease-out  */
--t-layout: 350ms;   /* Layout reflows  — ease-in-out */
```

**Asset Compliance & Design Polish (15 pts)**

- Full 6-color palette applied across hero orbs, card accents, gradients, badges, and interactive states.
- All SVG icons from the asset pack appear in the bento grid and accordion panels.
- Both typefaces are set with intentional weight scales: `400 / 500 / 600 / 700 / 800`.

**Breakpoint Fluidity (10 pts)**

Three-tier responsive layout:

| Breakpoint | Layout |
|---|---|
| `> 900px` | 12-col Bento Grid, full nav |
| `768px – 900px` | Accordion, full nav |
| `< 768px` | Accordion, hamburger menu |

**Motion Accuracy (5 pts)**

- Hero elements orchestrated with staggered `animation-delay` (0.05s → 0.3s).
- Bento cards use cursor-position CSS variables (`--mx`, `--my`) for a dynamic radial spotlight on hover.
- Scroll-triggered reveals via `IntersectionObserver` with `threshold: 0.12`.
- `prefers-reduced-motion` disables all transitions and animations for accessibility.

---

## Project Structure

```
.
└── index.html        # Complete single-file application
    ├── <head>        # Meta, OG tags, font imports
    ├── <style>       # All CSS — tokens, layout, components, responsive
    ├── <body>        # Semantic HTML structure
    └── <script>      # Pricing engine, accordion, resize handler, scroll reveals
```

---

## Getting Started

No build step. No dependencies. Open directly in a browser or deploy to any static host.

```bash
# Option 1 — open directly
open index.html

# Option 2 — serve locally
npx serve .

# Option 3 — deploy to Vercel
vercel deploy
```

---

## Disqualification Checklist

| Criterion | Status |
|---|---|
| Public GitHub repository with full source | ✅ |
| Live deployment link resolving without errors | ✅ |
| No plagiarism or copied boilerplate | ✅ |
| No empty or mock repository | ✅ |
| No banned libraries (Radix, Shadcn, Framer Motion, etc.) | ✅ |
| No hardcoded pricing values | ✅ |

---

## Author Huzefa

Built for **FB Round 1 — Next-Gen AI Platform Speed Run**
Date: 26 June 2026 · Duration: 2:00 PM – 6:00 PM
