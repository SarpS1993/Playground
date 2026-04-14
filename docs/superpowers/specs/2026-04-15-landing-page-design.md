# Landing Page Integration — Design Spec
**Date:** 2026-04-15  
**Project:** ING Investment Landscape Dashboard  
**Output file:** `output/ing_investment_landscape_with_landingpage.html`

---

## Overview

A single self-contained HTML file that opens with a professional "Instrument Panel" landing page, then seamlessly exposes the full existing dashboard below. No page reloads. The original file (`output/ing_investment_landscape.html`) is never modified.

---

## Aesthetic Direction: "Instrument Panel" (Data-Visualization Forward)

Full-viewport hero that functions as a live data canvas. Color palette, fonts, and CSS variables are identical to the dashboard — the landing page is a natural extension of the same design system, not a separate identity. The transition from landing to dashboard is continuous.

---

## Architecture

### File structure
- Single HTML file, inline CSS and JS
- Structure: `<landing-section>` → `<dashboard-wrapper>` (full original dashboard content)
- The original dashboard HTML/CSS/JS is embedded verbatim inside `<dashboard-wrapper>`
- No iframes, no external dependencies beyond the existing Google Fonts URL

### Navigation / transition
- Clicking "Enter Analysis →" button: smooth scroll to `#dashboard` anchor, or JS `scrollIntoView`
- Scroll-down chevron: same behavior
- Dashboard sticky nav (`position: sticky; top: 0`) continues to work correctly once user reaches that section
- Initial state: landing page fills the viewport; dashboard is below the fold

---

## Landing Page Sections (top to bottom)

### 1. Header (reused from dashboard)
- Identical to the dashboard `<header class="site-header">` — ING logo badge, brand title, meta badges (CONFIDENTIAL, date, FY2025)
- Reused directly; no duplication of styling needed

### 2. Hero (full viewport minus header height)
Two-column asymmetric layout:

**Left column (~55% width):**
- Eyebrow: `ING GROUP · FY2025 · 6 MARKETS` — JetBrains Mono, 10px, `--t3`, caps, letter-spacing 0.13em
- Headline: `"European Investment"` / `"Landscape Analysis"` — Bricolage Grotesque, ~72px, weight 800, `--t1`, letter-spacing −2px, two lines
- Subhead: `"A source-verified competitive benchmark across six European markets. Central bank household data. FY2025 figures throughout."` — Outfit, 16px, `--t2`, max-width 480px
- Stat pills row: three items separated by orange dot — `6 European Markets` · `Source-Verified Data` · `FY2025 Figures` — JetBrains Mono, 10px caps
- Glowing horizontal rule: 1px line, orange gradient, animates left-to-right on load
- CTA button: "Enter Analysis →" — orange background, glow, 48px height
- Scroll chevron: animated pulse below CTA

**Right column (~45% width):**
- Abstract SVG line chart at top (2 paths, animated stroke-dashoffset on load — market trajectory motif)
- 2×3 grid of country metric tiles: DE, BE, ES, IT, NL, PL
  - Each tile: flag SVG + country name + "Total Market" label + animated count-up number (€Xbn/€Xtrn)
  - Hover: subtle orange ambient glow
  - Data sourced from existing `DATA` const (the `inv` field per country)
- Soft orange radial bloom behind the grid (atmosphere layer)

**Diagonal separator:**
- CSS `clip-path` or pseudo-element at ~72° angle between left and right zones
- No hard border — light/glow creates the boundary

---

## Animation Sequence (staggered on `DOMContentLoaded`)

| Delay | Element | Animation |
|-------|---------|-----------|
| 0ms | Header | Already visible (no animation needed) |
| 150ms | Eyebrow | Fade in + translateY(8px → 0) |
| 300ms | Headline line 1 | Fade in + translateY(20px → 0) |
| 450ms | Headline line 2 | Fade in + translateY(20px → 0) |
| 550ms | Subhead | Fade in |
| 600ms | Country metric cards | Count-up numbers (requestAnimationFrame, 1200ms duration) |
| 650ms | SVG line chart | stroke-dashoffset animation |
| 900ms | Glowing rule | scaleX(0 → 1), transform-origin left |
| 1050ms | Stat pills | Fade in staggered |
| 1100ms | CTA + chevron | Fade in |

All animations use `cubic-bezier(0.4, 0, 0.2, 1)` (matching `--ease`). CSS `animation-fill-mode: both`.

---

## CSS

- All existing dashboard CSS embedded verbatim (no changes)
- Landing-specific styles added in a separate `<style>` block before the dashboard styles
- New CSS variables: none — reuse all `--` variables from the dashboard's `:root`
- Landing section uses class prefix `.lp-` to avoid collisions
- Responsive: at <900px viewport width, right canvas stacks below left column; count-up and chart still animate

---

## JavaScript

- All existing dashboard JS embedded verbatim (no changes)
- Landing-specific JS in a separate `<script>` block at end of `<body>`, after dashboard script
- Landing JS responsibilities:
  - Count-up animation for 6 country tiles (reads from `DATA` const already in scope)
  - SVG line chart stroke animation
  - Glowing rule scaleX animation trigger
  - CTA button scroll handler
  - Staggered reveal using `animation-delay` on `.lp-reveal` elements

---

## Content

| Element | Text |
|---------|------|
| Eyebrow | `ING GROUP · FY2025 · 6 MARKETS` |
| Headline | `European Investment` / `Landscape Analysis` |
| Subhead | `A source-verified competitive benchmark across six European markets. Central bank household data. FY2025 figures throughout.` |
| Stat 1 | `6 European Markets` |
| Stat 2 | `Source-Verified Data` |
| Stat 3 | `FY2025 Figures` |
| CTA | `Enter Analysis →` |
| Footer (landing) | none — dashboard footer reused |

---

## Constraints

- Original `output/ing_investment_landscape.html` — **never modified**
- Output: `output/ing_investment_landscape_with_landingpage.html`
- Self-contained: no external JS libraries; Google Fonts CDN link reused from original
- Dashboard sticky nav must function normally once scrolled into view
- All original dashboard functionality (tabs, charts, sort, animations, methodology page) must be 100% intact
