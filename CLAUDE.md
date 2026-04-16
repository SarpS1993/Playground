# ING Investments — European Investment Landscape

## Project Overview
Single-file HTML dashboard (`output/dashboard.html`) benchmarking ING's investment business across 6 European markets against local competitors. Built for senior management / board-level consumption.

## Key Files
| Path | Purpose |
|------|---------|
| `output/dashboard.html` | Main deliverable — single self-contained HTML dashboard |
| `data/research_notes.md` | Research log with source citations and data decisions |
| `data/research_data.json` | Compiled structured data (pre-HTML) |
| `data/abnamro_q4_2025.pdf` | ABN AMRO Q4 2025 Quarterly Report (downloaded) |
| `data/kbc_q4_2025_quarterly.pdf` | KBC Q4 2025 Quarterly Report (downloaded) |
| `data/unicredit_q4_2025.pdf` | UniCredit FY2025 press release (downloaded) |

## Editing Rules

> **IMPORTANT:** All changes must be made to `output/dashboard.html` only. Do **not** edit `docs/index.html` unless the user explicitly instructs it.

## Dashboard Architecture

### Data Layer (`const DATA`)
Six country objects (DE, BE, ES, IT, NL, PL), each containing:
- `market{}` — market-level AUM, AUC, population KPIs. Source priority: (1) central bank household accounts, (2) EFAMA Q4 2025 (fallback)
- `competitors[]` — competitor entries with `aum`, `auc`, `inv`, `clients`, `rev`, `period`, `url`

### ING Data (`const ING_DATA`)
Country-level internal figures (FY2025): Germany, Belgium, Spain, Italy, Netherlands, Poland.

### Key Functions
- `renderTabs()` — country nav + Data Quality tab
- `renderMarketCards()` — 6-country overview grid with count-up animation
- `renderDeepDive(code)` — KPI tiles + competitor table + bar chart for active country
- `renderTable(c)` — sortable competitor table with scope pills (country/estimated/group)
- `renderBars(c)` — animated horizontal bar chart
- `renderCaveats()` — Data Quality page with 4 caveat cards + scope matrix
- `getScope(comp)` — derives scope from `type` and `period` fields: `'country'|'estimated'|'group'`

### Navigation
- Country tabs (DE/BE/ES/IT/NL/PL) + **⚠ Data Quality** tab
- Quality tab shows/hides: market-overview-section, deep-dive, sources-section, caveats-section

## Data Standards

### Metric Definitions
- **`market.aum`** = household investment fund shares (mutual funds) only — F.52 in national accounts
- **`market.auc`** = household shares/equity + debt securities — F.511 + F.3 (direct market holdings / brokerage)
- **`market.inv`** = total invested balances = aum + auc (full demand-side retail investable market)

### Market Size Sources (central bank household financial accounts)
All figures as of latest available period; FX: Poland PLN→EUR at NBP 31 Dec 2025 rate EUR/PLN 4.2267.

| Country | AUM (funds) | AUC (shares+bonds) | Total inv. | Period | Source | Status |
|---------|------------|-------------------|-----------|--------|--------|--------|
| DE | €1,272.5bn | €2,045.4bn | €3,317.9bn | Q3 2025 | Bundesbank household stock positions | ✅ Official |
| BE | €307.0bn | €127.7bn | €434.7bn | Q2 2025 | NBB Financial Accounts 2025-II | ✅ Official |
| ES | €585.0bn | €246.2bn | €831.2bn | Q4 2025 | Banco de España household balance sheet | ✅ Official |
| IT | ~€835.8bn | ~€2,179.5bn | ~€3,015.4bn | Q3 2025 est. | Banca d'Italia 2023 stock + official flows through Q3 2025 | ⚠️ Estimated |
| NL | €128.5bn | €76.1bn | €204.7bn | Q3 2025 | DNB household securities holdings | ✅ Official |
| PL | €54.5bn (PLN 230.2bn) | €174.6bn (PLN 738.0bn) | €229.1bn (PLN 968.2bn) | Q3 2025 | NBP household financial assets | ✅ Official |

**Prior approach (EFAMA):** Fund assets *domiciled* in country — supply-side; dominated by institutional AIFs in NL (€750bn+) and DE (~€2,400bn). No longer used as primary source.

### Target Metric (competitor figures)
Client investment AUM = mutual funds + discretionary mandates + structured products.
Excludes: savings deposits, custody-only AUC (except brokers like DEGIRO/Fineco), institutional pension mandates.

### Scope Rules
- **Domestic-only firms** (Belfius, DekaBank, Union Investment, CaixaBank, PKO TFI, etc.): group total = country total ✓
- **Multi-country firms** (DWS, KBC, ABN AMRO, UniCredit, Azimut, flatexDEGIRO): must use country-specific figure
- **Flagged exceptions**: DWS (~40% estimate), UniCredit (group), Azimut (Italy+intl.), flatexDEGIRO (pan-EU)

### FX
Poland figures: PLN → EUR at NBP rate 31 Dec 2025 (EUR/PLN 4.2267)

### Scope Pills in Table
Derived by `getScope()` reading competitor `type` field:
- `type` contains `pan-eu` or `group aum/aim` → 🔴 Group ⚠
- `type` contains `global`, `+ intl`, `nl+intl` or `period` contains `est.` → 🟡 Est. ~
- Otherwise → 🟢 Country ✓

## Updating Data
To update competitor figures, edit the `const DATA` block (~lines 340–440):
```js
{name:"Competitor Name", type:"...", aum:XXX, auc:XXX, inv:XXX, clients:XXX, rev:XXX, period:"FY2025", url:"https://..."}
```
Set `null` for unavailable values — renders as "—" in table and is excluded from bar charts.

To add a new caveat, add an entry to `const CAVEATS` (~line 450).

## Next Steps
1. **UniCredit Italy-specific**: Seek Italy segment breakdown from FY2025 Annual Report (due ~May 2026)
2. **Azimut Italy/International split**: Request clarification from Assoreti or Azimut IR for confirmed Italy-only AUM
3. **Rabobank**: Investment AUM not found in public sources; may require direct outreach or DUFAS (Dutch fund association) data
4. **ABN AMRO**: NL-specific AUM removed (group WM includes Neuflize OBC France, Bethmann Germany) — data shows "—" until NL isolation becomes possible
5. **Argenta Belgium**: No public investment AUM disclosed; may be available from BEAMA statistics
6. **flatexDEGIRO NL isolation**: Country-level AUC breakdown may become available with ESMA reporting requirements in 2026
7. **BNP Paribas Fortis Belgium**: No investment AUM publicly disclosed — shows "—" until official figure available
8. **KBC Belgium segment**: Using ~90% of group AUM (€300bn × 90% = €270bn) per Q3 2025 quarterly report disclosure; confirm Belgium Business Unit AUM directly from annual report when published
