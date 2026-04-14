# ING Investments Dashboard — Ralph Loop Summary
**Date:** 2026-04-13  
**File:** `output/ing_investment_landscape.html`

---

## What Ralph Found

Two Ralph Loop rounds audited the dashboard against a senior board-level / McKinsey quality standard.

### Round 1 — Critical Issues
1. **Seven stale "2024" period labels** across title, header badge, section descriptions, table header, bar chart note, ING banner, and methodology text — all data was FY2025 but the UI still said 2024
2. **Missing Data Quality page** — the user explicitly requested a dedicated page for the 4 competitors with non-country-specific figures (DWS, UniCredit, Azimut, flatexDEGIRO); completely absent
3. **No scope signals in competitor table** — readers had no way to distinguish a confirmed country-specific figure (KBC Belgium €265bn) from a group-level estimate (UniCredit group €193.7bn) within the same table
4. **Mobile bar chart overflow** — fixed `180px` label column broke on small screens

### Round 2 — Residual Issue
- Sources accordion visible when Data Quality tab was active (wrong context bleed between views)

---

## What Was Improved

### 1. All Period Labels Corrected
Title, header badge, section descriptions, table note, bar chart note, ING aggregate banner — all updated to FY2025.

### 2. Scope Pill System Added to Competitor Table
New `getScope()` function derives data scope from existing `type` and `period` fields. No data changes required. Each non-ING competitor row now shows a colored pill:
- 🟢 **Country ✓** — confirmed country-specific figure
- 🟡 **Est. ~** — estimated or partial-international figure  
- 🔴 **Group ⚠** — known group-level figure shown for reference only

### 3. Data Quality Tab + Caveats Page
New `⚠ Data Quality` tab in the sticky navigation (amber styled, distinct from country tabs). Clicking it shows a dedicated page with:
- **Hero section** — summary counts: 2 group-level, 2 estimated, N confirmed, total competitor count
- **4 Caveat Cards** (DWS, UniCredit, Azimut, flatexDEGIRO) — each shows:
  - Shown figure vs. actual scope
  - Animated impact severity bar (High/Medium)
  - Analyst recommendation for how to use the figure
- **Scope Coverage Matrix** — all 30 competitors grouped by country, colored chips by scope type

### 4. Sources Section State Management
Added `id="sources-section"` and included it in all tab/card click handlers so it hides correctly when the Data Quality view is active.

### 5. Mobile Responsiveness
Bar chart label column changed from fixed `180px` to `minmax(100px,180px)` with a `600px` breakpoint override.

---

## Design Skill Verification

**Aesthetic**: "Precision financial intelligence" — dark navy (#070C16), ING orange (#FF6200) as primary accent, amber/red/green reserved for semantic data quality signals. Font trio: DM Serif Display (headlines) + DM Mono (data values) + DM Sans (body).

| Quality Criterion | Result |
|---|---|
| Looks like a real product | ✅ Dark fintech dashboard, not tutorial-grade |
| Clean layout + consistent spacing | ✅ 40px gutters, 12/16px gaps, coherent radius scale |
| Intuitive UX | ✅ Tab navigation, sort indicators, scope pills self-explain |
| Professional typography | ✅ DM Serif Display is distinctive and authoritative |
| Mobile responsive | ✅ Bar chart, nav scroll, header meta hidden on mobile |
| Micro-interactions | ✅ Count-up animations, bar fills, card hover translate+shadow |
| Small details | ✅ ING row orange-left-border, est. periods subdued, nulls → "—" |
| Data quality communication | ✅ Scope pills + dedicated caveats page with analyst recommendations |

---

## Data Sanity Checks

The prior research session verified and corrected all major data quality issues:
- **13 figures corrected** from group-level/wrong-period to country-specific FY2025
- **3 null entries populated** (mBank PL, Santander PL, BNP PL from Analizy.pl Dec 2025)
- **4 figures flagged** as still group-level (DWS, UniCredit, Azimut, flatexDEGIRO) with detailed caveats now visible in the dashboard

**Remaining data gaps (documented in CLAUDE.md Next Steps):**
- DekaBank: Q3 2025 used; FY2025 published today but not yet accessed
- Rabobank: NL investment AUM not found in public sources
- Argenta: No public investment AUM disclosed
