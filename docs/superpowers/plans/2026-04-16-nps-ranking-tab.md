# NPS Ranking Tab Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Add a "NPS Ranking" tab to `output/dashboard.html` that shows ING's Investment NPS scores vs. competitors across all 6 European markets, with a cross-country summary grid, per-country ranked bar chart, and a NPS vs. Balance dot plot for markets with sufficient dual-data.

**Architecture:** All changes are confined to `output/dashboard.html` (the single-file deliverable). The NPS data constant (`NPS_DATA`) is added near the other data constants. A new `nps-page` div is added alongside `priba-page`. A `handleNpsTab()` function mirrors `handlePribaTab()`. Rendering is split into three functions: `renderNpsPage()` (summary grid + country selector), `renderNpsCountryDetail(code)` (ranked bars), and `renderNpsScatter(code)` (dot plot, only for markets with ≥3 dual-data points). CSS classes are prefixed `nps-` throughout.

**Tech Stack:** Vanilla JS, inline CSS, SVG for the dot plot axes — no new libraries. Follows the existing dashboard patterns exactly (dark navy theme, ING orange highlight, JetBrains Mono / Outfit / Bricolage Grotesque fonts, CSS custom properties).

---

## Data Reference (March 2026 — most recent month, used throughout)

### NPS_DATA structure
Countries: BE, DE, IT, NL, PL, ES. Per country: array of `{name, nps, rank, n, hasBalance}` sorted by rank ascending. `nps` is a float. `n` is sample size. `hasBalance` = true when this competitor also has investment balance data in `DATA`.

### Raw values to hardcode

**Belgium** (Jan=row25-36, Feb=row25-36, Mar=row25-36 in sheet3):
- Mar 2026: Revolut 29.6 #1 n=115, Keytrade 25.3 #2 n=148, Argenta 16.8 #3 n=366, BNP Paribas Fortis 14.9 #4 n=532, KBC/CBC 13.3 #5 n=496, Belfius 12.9 #6 n=530, Crelan 8.8 #7 n=192, ING 8.6 **#8** n=275, DEGIRO 7.9 #9 n=206, Bolero 2.0 #10 n=419, Trade Republic 0.5 #11 n=233, eToro -0.5 #12 n=196
- Feb: ING 10.8 #6 | Jan: ING 11.5 #5

**Germany**:
- Mar 2026: Revolut 50.9 #1 n=106, Trade Republic 46.9 #2 n=343, ING 38.6 **#3** n=256, Sparkasse 36.6 #4 n=436, Comdirect 25.4 #5 n=108, Commerzbank 25.1 #6 n=109, Deutsche Bank 23.2 #7 n=149, VR-Banken 22.3 #8 n=217
- Feb: ING 40.7 #3 | Jan: ING 37.4 #2

**Italy**:
- Mar 2026: Fineco 63.2 #1 n=180, Revolut 53.2 #2 n=146, Banca Mediolanum 43.0 #3 n=127, UniCredit 39.0 #4 n=184, ING 38.9 **#5** n=104, BancoPosta 30.5 #6 n=293, Intesa Sanpaolo 26.9 #7 n=379, Bper 4.1 #8 n=404, Credit Agricole 3.9 #9 n=363, Banco BPM 1.6 #10 n=354, Credem 1.7 #11 n=316
- Feb: ING 38.7 #4 | Jan: ING 41.7 #3

**Netherlands**:
- Mar 2026: ING 21.1 **#1** n=272, DEGIRO 18.0 #2 n=232, ABN AMRO 13.8 #3 n=199, Rabobank 9.3 #4 n=213, Brand New Day 8.5 #5 n=300, EVI Van Lanschot 6.8 #6 n=165, Peaks 0.4 #7 n=202, Bux -3.9 #8 n=300
- Feb: ING 20.1 #1 | Jan: ING 22.6 #1

**Poland**:
- Mar 2026: Revolut 51.5 #1 n=151, mBank 34.7 #2 n=185, Pekao 34.7 #3 n=116, ING 33.4 **#4** n=163, PKO BP 30.4 #5 n=274
- Feb: ING 35.6 #3 | Jan: ING 37.7 #2

**Spain**:
- Mar 2026: Revolut 47.3 #1 n=309, Trade Republic 41.8 #2 n=220, ING 37.9 **#3** n=337, Banco Sabadell 32.5 #4 n=143, BBVA 27.9 #5 n=383, MyInvestor 25.1 #6 n=412, CaixaBank 21.6 #7 n=397, Banco Santander 20.9 #8 n=306, Interactive Brokers 16.7 #9 n=176, Indexa Capital 15.0 #10 n=250, DEGIRO 12.8 #11 n=264
- Feb: ING 36.2 #2 | Jan: ING 38.0 #2

### Dual-data competitors (NPS + balance in DATA) — for dot plot

**Belgium** (3 points — show dot plot):
- Belfius: NPS 12.9, inv €37.2bn
- Argenta: NPS 16.8, inv €19.1bn
- ING: NPS 8.6, inv €22bn

**Germany** (1 usable point — skip dot plot; asset managers dominate balance but aren't in NPS):
- ING: NPS 38.6, inv €135bn ← only one with both; no plot

**Italy** (5 points — richest, show dot plot):
- Fineco: NPS 63.2, inv €128.9bn
- Banca Mediolanum: NPS 43.0, inv €155.8bn
- UniCredit: NPS 39.0, inv €193.7bn (flagged group-level)
- ING: NPS 38.9, inv €6bn
- Intesa Sanpaolo: NPS 26.9, inv €425.4bn

**Netherlands** (3 points — show dot plot):
- DEGIRO: NPS 18.0, inv €95.5bn (flagged pan-EU)
- Rabobank: NPS 9.3, inv €73.1bn
- ING: NPS 21.1, inv €17bn

**Poland** (4 points — show dot plot):
- Revolut: NPS 51.5, inv null ← skip Revolut (no balance)
- mBank: NPS 34.7, inv €1.2bn
- Pekao: NPS 34.7, inv €9bn
- ING: NPS 33.4, inv €4bn
- PKO BP: NPS 30.4, inv €18.9bn
→ 4 usable points (mBank, Pekao, ING, PKO BP) — show dot plot

**Spain** (4 points — show dot plot):
- BBVA: NPS 27.9, inv €119.5bn
- CaixaBank: NPS 21.6, inv €202.9bn
- Banco Santander: NPS 20.9, inv €107.4bn
- ING: NPS 37.9, inv €22bn
→ 4 points — show dot plot

**Summary:** Show dot plot for BE, IT, NL, PL, ES. Skip DE (only ING has both data points).

---

## File to modify

- **Modify:** `output/dashboard.html`
  - Add `NPS_DATA` constant after `PRIBA_DATA`
  - Add `nps-page` div in HTML body (after `priba-page`)
  - Add NPS tab button in `renderTabs()`
  - Add `handleNpsTab()` function
  - Update all existing `handleXxxTab()` / `handleCardClick()` / `handleMShareClick()` functions to hide `nps-page`
  - Add `renderNpsPage()`, `renderNpsCountryDetail(code)`, `renderNpsScatter(code)` functions
  - Add NPS CSS styles (prefixed `nps-`)

---

## Task 1: Add NPS_DATA constant

**Files:**
- Modify: `output/dashboard.html` — add `NPS_DATA` after the `PRIBA_DATA` block (~line 2043)

- [ ] **Step 1: Locate insertion point**

Find the line `];` that closes `PRIBA_DATA` (currently around line 2043), then the blank line and `// ══ RENDER PRIBA PAGE ══` comment. Insert `NPS_DATA` between them.

- [ ] **Step 2: Insert the constant**

After the closing `];` of `PRIBA_DATA` and before `// ══════════════════════════════════════════` comment for RENDER PRIBA PAGE, insert:

```js
// ══════════════════════════════════════════
//  NPS DATA (Investment NPS · Source: ING Global NPS Tracking / Toluna)
//  Period: March 2026 (6-month rolling average Oct 2025 – Mar 2026)
//  "Low N" competitors excluded. Ranks are within reportable competitors only.
// ══════════════════════════════════════════
const NPS_DATA = {
  BE: {
    label: 'Belgium', period: 'Mar 2026',
    janNPS: 11.5, febNPS: 10.8, marNPS: 8.6,
    janRank: 5, febRank: 6, marRank: 8, totalRanked: 12,
    competitors: [
      {name:'Revolut',           nps:29.6, rank:1,  n:115, isING:false},
      {name:'Keytrade Bank',     nps:25.3, rank:2,  n:148, isING:false},
      {name:'Argenta',           nps:16.8, rank:3,  n:366, isING:false},
      {name:'BNP Paribas Fortis',nps:14.9, rank:4,  n:532, isING:false},
      {name:'KBC/CBC',           nps:13.3, rank:5,  n:496, isING:false},
      {name:'Belfius',           nps:12.9, rank:6,  n:530, isING:false},
      {name:'Crelan',            nps: 8.8, rank:7,  n:192, isING:false},
      {name:'ING',               nps: 8.6, rank:8,  n:275, isING:true},
      {name:'DEGIRO',            nps: 7.9, rank:9,  n:206, isING:false},
      {name:'Bolero',            nps: 2.0, rank:10, n:419, isING:false},
      {name:'Trade Republic',    nps: 0.5, rank:11, n:233, isING:false},
      {name:'eToro',             nps:-0.5, rank:12, n:196, isING:false}
    ],
    scatter: [
      {name:'Argenta',           nps:16.8, inv:19.1,  isING:false},
      {name:'Belfius',           nps:12.9, inv:37.2,  isING:false},
      {name:'ING',               nps: 8.6, inv:22.0,  isING:true}
    ],
    scatterNote: 'KBC and BNP Paribas Fortis excluded — no public investment balance data.'
  },
  DE: {
    label: 'Germany', period: 'Mar 2026',
    janNPS: 37.4, febNPS: 40.7, marNPS: 38.6,
    janRank: 2, febRank: 3, marRank: 3, totalRanked: 8,
    competitors: [
      {name:'Revolut',       nps:50.9, rank:1, n:106, isING:false},
      {name:'Trade Republic', nps:46.9, rank:2, n:343, isING:false},
      {name:'ING',           nps:38.6, rank:3, n:256, isING:true},
      {name:'Sparkasse',     nps:36.6, rank:4, n:436, isING:false},
      {name:'Comdirect',     nps:25.4, rank:5, n:108, isING:false},
      {name:'Commerzbank',   nps:25.1, rank:6, n:109, isING:false},
      {name:'Deutsche Bank', nps:23.2, rank:7, n:149, isING:false},
      {name:'VR-Banken',     nps:22.3, rank:8, n:217, isING:false}
    ],
    scatter: null,
    scatterNote: 'Dot plot unavailable for Germany — major balance holders (DWS, Union Investment, DekaBank) are asset managers not tracked in consumer NPS survey.'
  },
  IT: {
    label: 'Italy', period: 'Mar 2026',
    janNPS: 41.7, febNPS: 38.7, marNPS: 38.9,
    janRank: 3, febRank: 4, marRank: 5, totalRanked: 11,
    competitors: [
      {name:'Fineco',          nps:63.2, rank:1,  n:180, isING:false},
      {name:'Revolut',         nps:53.2, rank:2,  n:146, isING:false},
      {name:'Banca Mediolanum',nps:43.0, rank:3,  n:127, isING:false},
      {name:'UniCredit',       nps:39.0, rank:4,  n:184, isING:false},
      {name:'ING',             nps:38.9, rank:5,  n:104, isING:true},
      {name:'BancoPosta',      nps:30.5, rank:6,  n:293, isING:false},
      {name:'Intesa Sanpaolo', nps:26.9, rank:7,  n:379, isING:false},
      {name:'Bper',            nps: 4.1, rank:8,  n:404, isING:false},
      {name:'Credit Agricole', nps: 3.9, rank:9,  n:363, isING:false},
      {name:'Credem',          nps: 1.7, rank:10, n:316, isING:false},
      {name:'Banco BPM',       nps: 1.6, rank:11, n:354, isING:false}
    ],
    scatter: [
      {name:'Fineco',          nps:63.2, inv:128.9, isING:false},
      {name:'Banca Mediolanum',nps:43.0, inv:155.8, isING:false},
      {name:'UniCredit',       nps:39.0, inv:193.7, isING:false, caveat:'Group-level AuM+AuA'},
      {name:'ING',             nps:38.9, inv:6.0,   isING:true},
      {name:'Intesa Sanpaolo', nps:26.9, inv:425.4, isING:false}
    ],
    scatterNote: 'UniCredit shown at group level (Italy ~40–50% of total). Revolut and BancoPosta excluded — no public investment balance data.'
  },
  NL: {
    label: 'Netherlands', period: 'Mar 2026',
    janNPS: 22.6, febNPS: 20.1, marNPS: 21.1,
    janRank: 1, febRank: 1, marRank: 1, totalRanked: 8,
    competitors: [
      {name:'ING',            nps:21.1, rank:1, n:272, isING:true},
      {name:'DEGIRO',         nps:18.0, rank:2, n:232, isING:false},
      {name:'ABN AMRO',       nps:13.8, rank:3, n:199, isING:false},
      {name:'Rabobank',       nps: 9.3, rank:4, n:213, isING:false},
      {name:'Brand New Day',  nps: 8.5, rank:5, n:300, isING:false},
      {name:'EVI Van Lanschot',nps:6.8, rank:6, n:165, isING:false},
      {name:'Peaks',          nps: 0.4, rank:7, n:202, isING:false},
      {name:'Bux',            nps:-3.9, rank:8, n:300, isING:false}
    ],
    scatter: [
      {name:'ING',     nps:21.1, inv:17.0,  isING:true},
      {name:'DEGIRO',  nps:18.0, inv:95.5,  isING:false, caveat:'Pan-EU AUC (NL ~15–20%)'},
      {name:'Rabobank',nps: 9.3, inv:73.1,  isING:false}
    ],
    scatterNote: 'ABN AMRO excluded — no NL-specific balance published. DEGIRO shown at pan-EU AUC level; NL portion estimated at €14–19bn.'
  },
  PL: {
    label: 'Poland', period: 'Mar 2026',
    janNPS: 37.7, febNPS: 35.6, marNPS: 33.4,
    janRank: 2, febRank: 3, marRank: 4, totalRanked: 5,
    competitors: [
      {name:'Revolut', nps:51.5, rank:1, n:151, isING:false},
      {name:'mBank',   nps:34.7, rank:2, n:185, isING:false},
      {name:'Pekao',   nps:34.7, rank:3, n:116, isING:false},
      {name:'ING',     nps:33.4, rank:4, n:163, isING:true},
      {name:'PKO BP',  nps:30.4, rank:5, n:274, isING:false}
    ],
    scatter: [
      {name:'mBank',  nps:34.7, inv:1.2,  isING:false},
      {name:'Pekao',  nps:34.7, inv:9.0,  isING:false},
      {name:'ING',    nps:33.4, inv:4.0,  isING:true},
      {name:'PKO BP', nps:30.4, inv:18.9, isING:false}
    ],
    scatterNote: 'Revolut excluded — no public investment balance data.'
  },
  ES: {
    label: 'Spain', period: 'Mar 2026',
    janNPS: 38.0, febNPS: 36.2, marNPS: 37.9,
    janRank: 2, febRank: 2, marRank: 3, totalRanked: 11,
    competitors: [
      {name:'Revolut',           nps:47.3, rank:1,  n:309, isING:false},
      {name:'Trade Republic',    nps:41.8, rank:2,  n:220, isING:false},
      {name:'ING',               nps:37.9, rank:3,  n:337, isING:true},
      {name:'Banco Sabadell',    nps:32.5, rank:4,  n:143, isING:false},
      {name:'BBVA',              nps:27.9, rank:5,  n:383, isING:false},
      {name:'MyInvestor',        nps:25.1, rank:6,  n:412, isING:false},
      {name:'CaixaBank',         nps:21.6, rank:7,  n:397, isING:false},
      {name:'Banco Santander',   nps:20.9, rank:8,  n:306, isING:false},
      {name:'Interactive Brokers',nps:16.7,rank:9,  n:176, isING:false},
      {name:'Indexa Capital',    nps:15.0, rank:10, n:250, isING:false},
      {name:'DEGIRO',            nps:12.8, rank:11, n:264, isING:false}
    ],
    scatter: [
      {name:'ING',             nps:37.9, inv:22.0,   isING:true},
      {name:'BBVA',            nps:27.9, inv:119.5,  isING:false},
      {name:'CaixaBank',       nps:21.6, inv:202.9,  isING:false},
      {name:'Banco Santander', nps:20.9, inv:107.4,  isING:false}
    ],
    scatterNote: 'Revolut, Trade Republic, MyInvestor, Interactive Brokers, Indexa Capital, DEGIRO excluded — no public investment balance data. Banco Sabadell excluded — AUM data not isolated.'
  }
};
```

- [ ] **Step 3: Verify the file still opens without JS errors**

Open `output/dashboard.html` in a browser (or check console) — no errors expected since this is a pure data addition.

---

## Task 2: Add `nps-page` div to HTML body

**Files:**
- Modify: `output/dashboard.html` — add `nps-page` div alongside `priba-page`

- [ ] **Step 1: Find the priba-page div**

Locate (around line 1564):
```html
<div class="priba-page scroll-target" id="priba-page" style="display:none"></div>
```

- [ ] **Step 2: Insert nps-page immediately after**

```html
<div class="nps-page scroll-target" id="nps-page" style="display:none"></div>
```

---

## Task 3: Add NPS tab button to `renderTabs()`

**Files:**
- Modify: `output/dashboard.html` — `renderTabs()` function (~line 2187)

- [ ] **Step 1: Locate the PriBa tab button line**

Find:
```js
const pbActive = state.view === 'priba' ? ' active' : '';
```

- [ ] **Step 2: Add npsActive variable and button**

Replace:
```js
const pbActive = state.view === 'priba' ? ' active' : '';
```
With:
```js
const pbActive = state.view === 'priba' ? ' active' : '';
const npsActive = state.view === 'nps' ? ' active' : '';
```

Then find the line:
```js
<button class="country-tab priba-tab${pbActive}" onclick="handlePribaTab()">🏦 PriBa Ranking</button>
```
And add immediately after it:
```js
<button class="country-tab nps-tab${npsActive}" onclick="handleNpsTab()">📊 NPS Ranking</button>
```

---

## Task 4: Add `handleNpsTab()` and update existing handlers

**Files:**
- Modify: `output/dashboard.html` — JS section near `handlePribaTab()`

- [ ] **Step 1: Add handleNpsTab() after handlePribaTab()**

After the closing `}` of `handlePribaTab()`, insert:

```js
function handleNpsTab() {
  state.view = 'nps';
  state.npsCode = state.npsCode || 'DE';
  document.getElementById('market-overview-section').style.display = 'none';
  document.getElementById('mshare-section').style.display = 'none';
  document.getElementById('deep-dive').style.display = 'none';
  document.getElementById('sources-section').style.display = 'none';
  document.getElementById('caveats-section').style.display = 'none';
  document.getElementById('sources-page').style.display = 'none';
  document.getElementById('methodology-page').style.display = 'none';
  document.getElementById('update-log-page').style.display = 'none';
  document.getElementById('priba-page').style.display = 'none';
  document.getElementById('nps-page').style.display = 'block';
  renderTabs();
  renderNpsPage();
  scrollToDashboard();
}
```

- [ ] **Step 2: Add `state.npsCode` to the state object**

Find:
```js
const state = {
  activeCode: 'DE',
  sortKey: 'aum',
  sortDir: 'desc',
  barsAnimated: {},
  view: 'country'
};
```
Replace with:
```js
const state = {
  activeCode: 'DE',
  sortKey: 'aum',
  sortDir: 'desc',
  barsAnimated: {},
  view: 'country',
  npsCode: 'DE'
};
```

- [ ] **Step 3: Hide nps-page in all existing tab handlers**

For each of these functions: `handleMethodologyTab()`, `handleQualityTab()`, `handleSourcesTab()`, `handleUpdateLogTab()`, `handleCardClick()`, `handleMShareClick()` — add the following line alongside the other `style.display = 'none'` lines:

```js
document.getElementById('nps-page').style.display = 'none';
```

Also in `handlePribaTab()`, add:
```js
document.getElementById('nps-page').style.display = 'none';
```

---

## Task 5: Add NPS CSS styles

**Files:**
- Modify: `output/dashboard.html` — `<style>` block, append before closing `</style>`

- [ ] **Step 1: Find the end of the existing CSS block**

Locate the `</style>` tag (after all existing CSS rules).

- [ ] **Step 2: Insert NPS styles before `</style>`**

```css
/* ─── NPS PAGE ─── */
.nps-page{margin-top:0}
.nps-summary-grid{display:grid;grid-template-columns:repeat(3,1fr);gap:18px;margin-bottom:40px}
@media(max-width:1100px){.nps-summary-grid{grid-template-columns:repeat(2,1fr)}}
@media(max-width:680px){.nps-summary-grid{grid-template-columns:1fr}}

.nps-card{
  background:linear-gradient(145deg,var(--bg2) 0%,var(--bg3) 100%);
  border:1px solid var(--border);border-radius:var(--r16);padding:24px;
  cursor:pointer;transition:all 250ms var(--ease);position:relative;overflow:hidden;
}
.nps-card::before{
  content:'';position:absolute;top:0;left:0;right:0;height:2px;
  background:transparent;transition:background 250ms var(--ease);
}
.nps-card:hover{border-color:rgba(255,85,0,0.28);transform:translateY(-4px);
  box-shadow:0 14px 40px rgba(0,0,0,0.45),0 0 0 1px rgba(255,85,0,0.15)}
.nps-card:hover::before{background:linear-gradient(90deg,var(--orange),#FF9500)}
.nps-card.nps-active{border-color:rgba(255,85,0,0.35);background:linear-gradient(145deg,rgba(255,85,0,0.07) 0%,var(--bg3) 100%)}
.nps-card.nps-active::before{background:linear-gradient(90deg,var(--orange),#FF9500);box-shadow:0 0 10px rgba(255,85,0,0.4)}

.nps-card-header{display:flex;align-items:center;justify-content:space-between;margin-bottom:18px}
.nps-card-identity{display:flex;align-items:center;gap:10px}
.nps-card-name{font-family:var(--fd);font-size:16px;font-weight:600;color:var(--t1)}
.nps-period-chip{font-family:var(--fm);font-size:9px;color:var(--t3);letter-spacing:0.08em;
  background:var(--bg4);border:1px solid var(--border2);border-radius:4px;padding:3px 7px}

.nps-score-row{display:flex;align-items:baseline;gap:10px;margin-bottom:14px}
.nps-score-big{font-family:var(--fm);font-size:38px;font-weight:400;line-height:1;letter-spacing:-2px;color:var(--orange-text)}
.nps-rank-pill{
  font-family:var(--fm);font-size:11px;font-weight:500;
  padding:3px 10px;border-radius:20px;border:1px solid;
}
.nps-rank-pill.rank-top1{color:#00FF88;border-color:rgba(0,255,136,0.35);background:rgba(0,255,136,0.08)}
.nps-rank-pill.rank-top3{color:var(--cyan);border-color:rgba(0,212,255,0.3);background:rgba(0,212,255,0.07)}
.nps-rank-pill.rank-mid{color:var(--amber);border-color:rgba(255,184,0,0.3);background:rgba(255,184,0,0.07)}
.nps-rank-pill.rank-low{color:var(--red);border-color:rgba(255,45,120,0.3);background:rgba(255,45,120,0.07)}

.nps-trend-row{display:flex;align-items:center;gap:8px;margin-bottom:14px}
.nps-trend-label{font-family:var(--fm);font-size:9px;color:var(--t3);letter-spacing:0.1em;text-transform:uppercase}
.nps-trend-months{display:flex;align-items:center;gap:6px;font-family:var(--fm);font-size:11px}
.nps-month-chip{color:var(--t3);font-size:10px}
.nps-month-val{color:var(--t2)}
.nps-delta{font-family:var(--fm);font-size:11px;margin-left:4px}
.nps-delta.pos{color:#4ade80}.nps-delta.neg{color:#f87171}.nps-delta.flat{color:var(--t3)}

.nps-mini-bar-track{height:4px;background:rgba(255,255,255,0.06);border-radius:2px;overflow:hidden}
.nps-mini-bar-fill{height:100%;border-radius:2px;background:linear-gradient(90deg,var(--orange),#FF9500)}

/* ─── NPS DETAIL ─── */
.nps-detail-section{margin-top:0}
.nps-detail-header{display:flex;align-items:baseline;justify-content:space-between;margin-bottom:26px;gap:16px}
.nps-detail-title{font-family:var(--fd);font-size:22px;font-weight:700;color:var(--t1);letter-spacing:-0.3px}
.nps-detail-meta{font-family:var(--fm);font-size:10px;color:var(--t3);letter-spacing:0.1em}

.nps-bar-list{display:flex;flex-direction:column;gap:11px;margin-bottom:38px}
.nps-bar-row{display:grid;grid-template-columns:32px minmax(120px,200px) 1fr 60px;align-items:center;gap:12px}
.nps-bar-rank{font-family:var(--fm);font-size:11px;color:var(--t3);text-align:right}
.nps-bar-name{font-family:var(--fb);font-size:13px;color:var(--t2);overflow:hidden;text-overflow:ellipsis;white-space:nowrap}
.nps-bar-track{height:9px;background:rgba(255,255,255,0.04);border-radius:5px;overflow:hidden;position:relative}
.nps-bar-zero{position:absolute;top:0;bottom:0;width:1px;background:rgba(255,255,255,0.12)}
.nps-bar-fill-pos{height:100%;border-radius:5px;background:linear-gradient(90deg,#1855a0,var(--cyan));transition:width 900ms cubic-bezier(.4,0,.2,1);width:0%}
.nps-bar-fill-neg{height:100%;border-radius:5px;background:linear-gradient(90deg,var(--red),#ff6b9d);transition:width 900ms cubic-bezier(.4,0,.2,1);width:0%;position:absolute;right:0}
.nps-bar-val{font-family:var(--fm);font-size:11px;color:var(--t2);text-align:right;white-space:nowrap}
.nps-bar-row.nps-ing-row{
  background:rgba(255,85,0,0.055);border-radius:8px;
  border-left:3px solid var(--orange);padding-left:6px;
  box-shadow:inset 0 0 20px rgba(255,85,0,0.03);
}
.nps-bar-row.nps-ing-row .nps-bar-name{color:var(--t1);font-weight:600}
.nps-bar-row.nps-ing-row .nps-bar-fill-pos{background:linear-gradient(90deg,var(--orange),#FF9500)}
.nps-n-note{font-family:var(--fm);font-size:9px;color:var(--t3);margin-top:4px;letter-spacing:0.06em}

/* ─── NPS SCATTER ─── */
.nps-scatter-wrap{
  background:linear-gradient(145deg,var(--bg2),var(--bg3));
  border:1px solid var(--border);border-radius:var(--r16);padding:28px;margin-top:32px;
}
.nps-scatter-title{font-family:var(--fd);font-size:17px;font-weight:600;color:var(--t1);margin-bottom:6px}
.nps-scatter-sub{font-family:var(--fm);font-size:10px;color:var(--t3);margin-bottom:20px;line-height:1.6}
.nps-scatter-svg{width:100%;overflow:visible;display:block}
.nps-scatter-note{font-family:var(--fm);font-size:9px;color:var(--t4);margin-top:14px;line-height:1.6;letter-spacing:0.04em}
.nps-no-scatter{font-family:var(--fm);font-size:11px;color:var(--t3);line-height:1.7;
  padding:20px 24px;background:var(--bg2);border:1px solid var(--border);border-radius:var(--r12);margin-top:32px}
```

---

## Task 6: Implement `renderNpsPage()` — summary grid + detail wiring

**Files:**
- Modify: `output/dashboard.html` — JS section, add after `handleNpsTab()`

- [ ] **Step 1: Insert renderNpsPage() function**

```js
function renderNpsPage() {
  const page = document.getElementById('nps-page');
  const codes = ['BE','DE','IT','NL','PL','ES'];

  function rankPillClass(rank, total) {
    if (rank === 1) return 'rank-top1';
    if (rank <= 3) return 'rank-top3';
    if (rank / total <= 0.5) return 'rank-mid';
    return 'rank-low';
  }

  function deltaStr(a, b) {
    // a = earlier, b = later
    const d = b - a;
    if (Math.abs(d) < 0.05) return '<span class="nps-delta flat">—</span>';
    const sign = d > 0 ? '+' : '';
    const cls = d > 0 ? 'pos' : 'neg';
    return `<span class="nps-delta ${cls}">${sign}${d.toFixed(1)}</span>`;
  }

  const cards = codes.map(code => {
    const d = NPS_DATA[code];
    const isActive = state.npsCode === code;
    const pillCls = rankPillClass(d.marRank, d.totalRanked);
    // mini progress bar: ING NPS relative to max NPS in this market
    const maxNps = Math.max(...d.competitors.map(c => c.nps));
    const barPct = Math.max(0, (d.marNPS / maxNps) * 100).toFixed(1);
    const delta = deltaStr(d.janNPS, d.marNPS);
    return `
    <div class="nps-card${isActive ? ' nps-active' : ''}" onclick="npsSelectCountry('${code}')">
      <div class="nps-card-header">
        <div class="nps-card-identity">
          ${flag(code, 'sm')}
          <div class="nps-card-name">${d.label}</div>
        </div>
        <span class="nps-period-chip">${d.period}</span>
      </div>
      <div class="nps-score-row">
        <span class="nps-score-big">${d.marNPS.toFixed(1)}</span>
        <span class="nps-rank-pill ${pillCls}">#${d.marRank} of ${d.totalRanked}</span>
      </div>
      <div class="nps-trend-row">
        <span class="nps-trend-label">3-month</span>
        <div class="nps-trend-months">
          <span class="nps-month-chip">Jan</span>
          <span class="nps-month-val">${d.janNPS.toFixed(1)}</span>
          <span class="nps-month-chip">→ Feb</span>
          <span class="nps-month-val">${d.febNPS.toFixed(1)}</span>
          <span class="nps-month-chip">→ Mar</span>
          <span class="nps-month-val">${d.marNPS.toFixed(1)}</span>
          ${delta}
        </div>
      </div>
      <div class="nps-mini-bar-track">
        <div class="nps-mini-bar-fill" style="width:${barPct}%"></div>
      </div>
    </div>`;
  }).join('');

  page.innerHTML = `
    <div class="section-head" style="margin-top:0">
      <div>
        <div class="section-eyebrow" style="color:var(--cyan)">Customer Perception · ING Global NPS Tracking / Toluna</div>
        <div class="section-title">Investment NPS Ranking</div>
      </div>
      <div class="section-desc">6-month rolling average · March 2026 · Select a market for full ranking and NPS vs. balance analysis</div>
    </div>
    <div class="nps-summary-grid">${cards}</div>
    <div id="nps-detail"></div>`;

  renderNpsDetail(state.npsCode);
}

function npsSelectCountry(code) {
  state.npsCode = code;
  renderNpsPage();
}
```

---

## Task 7: Implement `renderNpsDetail(code)` — ranked bar chart

**Files:**
- Modify: `output/dashboard.html` — JS section, add after `renderNpsPage()`

- [ ] **Step 1: Insert renderNpsDetail() function**

```js
function renderNpsDetail(code) {
  const detail = document.getElementById('nps-detail');
  if (!detail) return;
  const d = NPS_DATA[code];
  const competitors = d.competitors;

  // Find range for bar scaling
  const allNps = competitors.map(c => c.nps);
  const maxNps = Math.max(...allNps);
  const minNps = Math.min(...allNps, 0);
  const range = maxNps - minNps;
  // Zero line position as percentage of track width
  const zeroPct = ((-minNps) / range * 100).toFixed(1);

  const rows = competitors.map(c => {
    const isING = c.isING;
    const ingCls = isING ? ' nps-ing-row' : '';
    const nameStr = isING ? `${c.name} <span style="display:inline-flex;align-items:center;background:linear-gradient(90deg,var(--orange),#FF8C00);color:#fff;font-size:9px;font-family:var(--fd);font-weight:700;padding:1px 7px 2px;border-radius:3px;margin-left:6px;letter-spacing:0.08em;vertical-align:middle">ING</span>` : c.name;
    const valStr = c.nps >= 0 ? `+${c.nps.toFixed(1)}` : c.nps.toFixed(1);
    const nStr = `n=${c.n}`;

    let barHtml;
    if (c.nps >= 0) {
      const pct = (c.nps / maxNps * (100 - parseFloat(zeroPct))).toFixed(1);
      barHtml = `<div class="nps-bar-track">
        <div class="nps-bar-zero" style="left:${zeroPct}%"></div>
        <div class="nps-bar-fill-pos" style="width:0%;margin-left:${zeroPct}%" data-npspct="${pct}" data-npsmargin="${zeroPct}"></div>
      </div>`;
    } else {
      const pct = (Math.abs(c.nps) / Math.abs(minNps) * parseFloat(zeroPct)).toFixed(1);
      barHtml = `<div class="nps-bar-track" style="position:relative">
        <div class="nps-bar-zero" style="left:${zeroPct}%"></div>
        <div class="nps-bar-fill-neg" style="width:0%;right:${(100 - parseFloat(zeroPct)).toFixed(1)}%" data-npspct="${pct}" data-npsneg="1"></div>
      </div>`;
    }

    return `<div class="nps-bar-row${ingCls}">
      <div class="nps-bar-rank">#${c.rank}</div>
      <div class="nps-bar-name">${nameStr}<div class="nps-n-note">${nStr}</div></div>
      ${barHtml}
      <div class="nps-bar-val">${valStr}</div>
    </div>`;
  }).join('');

  detail.innerHTML = `
    <div class="nps-detail-section">
      <div class="nps-detail-header">
        <div class="nps-detail-title">${flag(code,'sm')} ${d.label} — Full NPS Ranking</div>
        <div class="nps-detail-meta">Source: ING Global NPS Tracking / Toluna · ${d.period} · Low-N competitors excluded</div>
      </div>
      <div class="nps-bar-list">${rows}</div>
      ${renderNpsScatter(code)}
    </div>`;

  // Animate bars on next frame
  requestAnimationFrame(() => {
    document.querySelectorAll('.nps-bar-fill-pos[data-npspct]').forEach(el => {
      const pct = el.dataset.npspct;
      const margin = el.dataset.npsmargin;
      el.style.width = pct + '%';
      el.style.marginLeft = margin + '%';
    });
    document.querySelectorAll('.nps-bar-fill-neg[data-npspct]').forEach(el => {
      const pct = el.dataset.npspct;
      const zeroPctVal = (100 - parseFloat(el.style.right)).toFixed(1);
      el.style.width = pct + '%';
    });
  });
}
```

---

## Task 8: Implement `renderNpsScatter(code)` — NPS vs. Balance dot plot

**Files:**
- Modify: `output/dashboard.html` — JS section, add after `renderNpsDetail()`

- [ ] **Step 1: Insert renderNpsScatter() function**

The dot plot is an inline SVG. Points are plotted with NPS on X axis and log(inv balance) on Y axis (log scale handles the wide range from €1.2bn to €425bn). ING dot is orange, others are cyan. Dots with a `caveat` field get a dashed border. Axis labels are rendered as SVG text.

```js
function renderNpsScatter(code) {
  const d = NPS_DATA[code];
  if (!d.scatter || d.scatter.length < 2) {
    return `<div class="nps-no-scatter">
      <strong style="color:var(--t2)">NPS vs. Investment Balance</strong><br>
      ${d.scatterNote}
    </div>`;
  }

  const pts = d.scatter;
  const W = 520, H = 280;
  const PAD = {top:24, right:32, bottom:48, left:72};
  const plotW = W - PAD.left - PAD.right;
  const plotH = H - PAD.top - PAD.bottom;

  const npsVals = pts.map(p => p.nps);
  const invVals = pts.map(p => p.inv);
  const npsMin = Math.min(...npsVals) - 5;
  const npsMax = Math.max(...npsVals) + 5;
  // Log scale for Y (investment balance)
  const logMin = Math.log10(Math.max(0.5, Math.min(...invVals) * 0.7));
  const logMax = Math.log10(Math.max(...invVals) * 1.4);

  function xPos(nps) {
    return PAD.left + ((nps - npsMin) / (npsMax - npsMin)) * plotW;
  }
  function yPos(inv) {
    return PAD.top + (1 - (Math.log10(inv) - logMin) / (logMax - logMin)) * plotH;
  }

  // Y-axis tick values (round powers/halves)
  function yTicks() {
    const ticks = [];
    for (let v = Math.ceil(logMin); v <= logMax; v += 0.5) {
      const val = Math.pow(10, v);
      if (val >= Math.pow(10, logMin) && val <= Math.pow(10, logMax)) {
        ticks.push(val);
      }
    }
    return ticks;
  }
  // X-axis ticks
  function xTicks() {
    const step = (npsMax - npsMin) > 40 ? 10 : 5;
    const ticks = [];
    for (let v = Math.ceil(npsMin / step) * step; v <= npsMax; v += step) ticks.push(v);
    return ticks;
  }

  const gridLines = yTicks().map(v => {
    const y = yPos(v);
    const label = v >= 100 ? Math.round(v) : v < 10 ? v.toFixed(1) : Math.round(v);
    return `<line x1="${PAD.left}" x2="${W - PAD.right}" y1="${y}" y2="${y}" stroke="rgba(255,255,255,0.06)" stroke-width="1"/>
      <text x="${PAD.left - 6}" y="${y + 4}" text-anchor="end" font-size="9" fill="#4e7090" font-family="JetBrains Mono,monospace">€${label}bn</text>`;
  }).join('');

  const xGridLines = xTicks().map(v => {
    const x = xPos(v);
    return `<line x1="${x}" x2="${x}" y1="${PAD.top}" y2="${H - PAD.bottom}" stroke="rgba(255,255,255,0.04)" stroke-width="1"/>
      <text x="${x}" y="${H - PAD.bottom + 14}" text-anchor="middle" font-size="9" fill="#4e7090" font-family="JetBrains Mono,monospace">${v}</text>`;
  }).join('');

  const dots = pts.map(p => {
    const cx = xPos(p.nps);
    const cy = yPos(p.inv);
    const r = p.isING ? 7 : 5.5;
    const fill = p.isING ? 'rgba(255,85,0,0.9)' : 'rgba(0,212,255,0.7)';
    const stroke = p.isING ? '#FF5500' : (p.caveat ? '#FFB800' : '#00D4FF');
    const strokeW = p.caveat ? 1.5 : (p.isING ? 2 : 1);
    const strokeDash = p.caveat ? '3,2' : 'none';
    // Label positioning: push label above for ING to avoid overlap
    const labelDy = p.isING ? -12 : -10;
    const nameShort = p.name.length > 14 ? p.name.slice(0, 13) + '…' : p.name;
    const caveatMark = p.caveat ? ' ⚠' : '';
    return `<g>
      <circle cx="${cx}" cy="${cy}" r="${r}" fill="${fill}" stroke="${stroke}" stroke-width="${strokeW}" stroke-dasharray="${strokeDash}" opacity="0.92"/>
      <text x="${cx}" y="${cy + labelDy}" text-anchor="middle" font-size="9.5" fill="${p.isING ? '#FF7A3D' : '#8fb8d8'}" font-family="Outfit,sans-serif" font-weight="${p.isING ? 600 : 400}">${nameShort}${caveatMark}</text>
    </g>`;
  }).join('');

  // Axis labels
  const axisLabels = `
    <text x="${PAD.left + plotW/2}" y="${H - 4}" text-anchor="middle" font-size="10" fill="#4e7090" font-family="JetBrains Mono,monospace">Investment NPS →</text>
    <text x="12" y="${PAD.top + plotH/2}" text-anchor="middle" font-size="10" fill="#4e7090" font-family="JetBrains Mono,monospace" transform="rotate(-90,12,${PAD.top + plotH/2})">Inv. Balance (€bn, log) →</text>`;

  const caveatLegend = pts.some(p => p.caveat)
    ? `<text x="${W - PAD.right}" y="${PAD.top + plotH + 28}" text-anchor="end" font-size="8.5" fill="#FFB800" font-family="JetBrains Mono,monospace">⚠ = scope caveat (see Data Quality tab)</text>`
    : '';

  const svg = `<svg class="nps-scatter-svg" viewBox="0 0 ${W} ${H}" style="max-width:${W}px">
    ${gridLines}${xGridLines}${dots}${axisLabels}${caveatLegend}
  </svg>`;

  return `<div class="nps-scatter-wrap">
    <div class="nps-scatter-title">NPS vs. Investment Balance</div>
    <div class="nps-scatter-sub">Each dot = one competitor with both NPS and published investment balance data. Higher-right = stronger NPS and larger balance. ING highlighted in orange.</div>
    ${svg}
    <div class="nps-scatter-note">⚠ ${d.scatterNote}</div>
  </div>`;
}
```

---

## Task 9: Add Update Log entry and commit

**Files:**
- Modify: `output/dashboard.html` — `UPDATE_LOG` array

- [ ] **Step 1: Add entry at top of UPDATE_LOG**

Find the `const UPDATE_LOG = [` line and insert as the first entry:

```js
{
  date: '2026-04-16',
  title: 'NPS Ranking Tab',
  desc: 'Added Investment NPS Ranking tab showing ING\'s NPS score and competitive rank across all 6 European markets (BE, DE, IT, NL, PL, ES). Source: ING Global NPS Tracking / Toluna, March 2026 (6-month rolling average). Features: cross-country summary grid with 3-month trend, per-country ranked bar chart, and NPS vs. Investment Balance dot plot for markets with sufficient dual-data (BE, IT, NL, PL, ES).'
},
```

- [ ] **Step 2: Verify full dashboard in browser**
- Click each country tab — no regressions
- Click NPS Ranking tab — summary grid appears
- Click each market card — detail panel updates, bars animate
- Dot plot renders for BE, IT, NL, PL, ES; "not available" message for DE
- Click PriBa Ranking tab — NPS page hides correctly
- Click Data Quality tab — NPS page hides correctly

- [ ] **Step 3: Commit**

```bash
git add output/dashboard.html
git commit -m "feat: add NPS Ranking tab with cross-country summary, ranked bars, and NPS vs. balance scatter"
```

---

## Self-Review

**Spec coverage:**
- ✅ Cross-country summary grid (2×3 cards, NPS score, rank, 3-month trend) — Task 6
- ✅ Per-country ranked bar chart with ING highlighted — Task 7
- ✅ NPS vs. Balance dot plot (markets with ≥2 dual-data points) — Task 8
- ✅ DE shows explanatory note instead of dot plot — Task 8 (`d.scatter === null`)
- ✅ Tab button in nav — Task 3
- ✅ All existing tabs hide nps-page — Task 4
- ✅ Dark navy theme, ING orange, existing font stack — Task 5 CSS
- ✅ Negative NPS bars render left of zero line — Task 7
- ✅ Low-N competitors excluded (not in NPS_DATA) — Task 1

**Placeholder scan:** No TBDs or TODOs. All code blocks are complete.

**Type consistency:**
- `state.npsCode` set in Task 4, read in Tasks 6 and 7 ✅
- `NPS_DATA[code].scatter` is `null` for DE, array for others — `renderNpsScatter` handles both via `if (!d.scatter || d.scatter.length < 2)` ✅
- `renderNpsDetail` calls `renderNpsScatter` inline ✅
- `npsSelectCountry(code)` sets `state.npsCode` and calls `renderNpsPage()` which calls `renderNpsDetail()` ✅
- Bar animation uses `data-npspct` / `data-npsmargin` attributes — set and read in same Task 7 ✅
