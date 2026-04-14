# Ralph Loop Findings — 2026-04-13

## Session Summary
Ran 2 Ralph Loop rounds + frontend-design skill verification on `output/ing_investment_landscape.html`.
Starting state: data was correct (FY2025 country-specific figures updated in prior session) but the dashboard had numerous presentation defects and was missing the requested caveats page.

---

## Round 1 Audit — Issues Found

### Critical Bugs (wrong information shown)
| Location | Bug | Fix Applied |
|---|---|---|
| `<title>` | "European Investment Landscape 2024" | → 2025 |
| Header badge | "Target: FY2025 — Data: Q4 2024" | → "FY2025 · Country-Specific Data" |
| Section description | "EFAMA Q4 2024" | → "EFAMA Q4 2025" |
| Table header note | "FY2024 annual reports" | → "FY2025" |
| Bar chart note | "FY2024 · AUM…" | → "FY2025 · AUM…" |
| ING banner | "FY2024 · Internal" | → "FY2025 · Internal Data" |
| Methodology note | "most FY2025 data unavailable as of April 2026" | Rewritten to reflect actual FY2025 data |

### Missing Feature
- **Data Quality / Caveats page** — explicitly requested by user; completely absent
- No way for a reader to understand that DWS, UniCredit, Azimut, flatexDEGIRO figures are not country-specific

### UX/Design Gaps
- No visual scope indicators in competitor table (reader couldn't tell group vs. country figures)
- Bar chart `bar-name` column was fixed `180px` — overflowed on mobile
- No `id` on sources section (couldn't be hidden independently)

---

## Round 1 Improvements Applied

### New: Data Quality Tab
Added `⚠ Data Quality` tab to the sticky navigation (styled amber to stand out from country tabs).
Clicking it:
- Hides market overview, deep dive, sources sections
- Shows dedicated caveats section with hero summary + 4 caveat cards + scope matrix

### New: `const CAVEATS` Data Structure
```js
{name, country, scope:'group'|'estimated', shown:{val,label,note}, actual:{val,label,note}, impact:'high'|'medium', impactPct, recommendation}
```
Four entries: DWS (estimated, medium), UniCredit (group, high), Azimut (estimated, medium), flatexDEGIRO (group, high).

### New: `getScope()` + `scopePill()` Functions
Derives scope from `type` and `period` fields — no data changes required.
Renders colored pill after competitor name in the table:
- 🟢 `Country ✓` (green)
- 🟡 `Est. ~` (amber)
- 🔴 `Group ⚠` (red)

### New: Caveats Page Content
- **Hero section**: summary stats (2 group, 2 estimated, X confirmed, total competitors)
- **4 caveat cards**: each shows "shown figure" vs "actual scope", impact bar (fills on load), analyst recommendation
- **Scope matrix**: all competitors grouped by country, colored chips by scope type

### Bar Chart Mobile Fix
`grid-template-columns: 180px 1fr 80px` → `minmax(100px,180px) 1fr 72px` with responsive override at 600px

---

## Round 2 Audit — Remaining Issues Found & Fixed

| Issue | Fix |
|---|---|
| Sources section visible when caveats tab active | Added `id="sources-section"`, toggle display in all tab handlers |
| `handleCardClick` didn't restore sources section | Added `sources-section` show/hide to all 3 handlers |

---

## Design Skill Verification

**Aesthetic direction**: "Precision financial intelligence" — dark navy/midnight, ING orange accent, DM Serif Display + DM Mono + DM Sans font stack. Consistent with existing brand identity.

**Quality bar assessment:**
- ✅ Looks like a real product — dark fintech dashboard aesthetic, not a tutorial project
- ✅ Clean layout — consistent 40px gutters, 12/16px gap grids, 8px/12px border radii
- ✅ Typography — DM Serif Display for headlines, DM Mono for data values, DM Sans for body; all characterful choices
- ✅ Color — dominant dark backgrounds with ING orange as single accent; amber/red/green reserved for semantic data quality signals
- ✅ Intuitive — tabs clearly labeled, scope pills immediately communicate data quality, sort indicators on table headers
- ✅ Mobile — bar chart responsive, nav scrolls horizontally, header meta hidden at ≤768px
- ✅ Micro-interactions — count-up animations on market card numbers, bar chart fills on render, caveat impact bars animate on page load, card hover states with translate + shadow
- ✅ Small details — ING row highlighted orange-left-border, "est." periods shown in subdued color, null values render as "—" not empty
- ✅ Data Quality page — amber accent distinct from orange, caveat cards have top border color coding (red=group, amber=estimated), scope matrix with clear legend

**Notable: The caveats page is the most differentiating addition** — directly addresses the user's original concern about misleading group-level data, with analyst-grade recommendations for each flagged competitor.

---

## Data Sanity Checks Performed (prior session)

| Country | Competitor | Before | After | Source |
|---|---|---|---|---|
| DE | DWS Group | €1,085bn (global) | €434bn (est. ~40% Germany) | Estimate; no public breakdown |
| DE | Union Investment | €526bn (Q3) | €535bn (FY2025) | Press release Jan 2026 |
| BE | KBC Group | €300bn (group) | €265bn (Belgium BU) | KBC Q4 2025 PDF p.61 |
| BE | BNP Paribas Fortis | null | €174bn (est.) | Press release Feb 2026 |
| ES | Santander Spain | null | €107.4bn | Q4 2025 financial report |
| ES | BBVA Spain | null | €119.5bn | 4Q25 Spain Business Areas |
| IT | Fideuram-ISPB | €420bn (est.) | €425.4bn (confirmed) | ISP FY2025 press release |
| IT | UniCredit | €408.5bn (AuM+AuC) | €193.7bn (AuM+AuA only; AuC excluded) | UniCredit FY2025 press release |
| NL | ABN AMRO | €396.9bn (total client assets) | €218.1bn (investment securities only) | ABN AMRO Q4 2025 PDF |
| NL | VLK | €158.9bn (group) | €48bn (NL Private Banking) | VLK 2025 annual results |
| PL | PKO TFI | €23bn (est.) | €18.9bn (confirmed) | Analizy.pl Dec 2025 |
| PL | Pekao TFI | €9bn (est.) | €9.0bn (confirmed) | Analizy.pl Dec 2025 |
| PL | mBank/Santander/BNP | null (all 3) | €1.2/€7.8/€2.4bn | Analizy.pl Dec 2025 |
