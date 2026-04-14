# Landing Page Integration — Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Create `output/ing_investment_landscape_with_landingpage.html` — a single self-contained HTML file that opens with an "Instrument Panel" landing page and exposes the full original dashboard below, without ever modifying the original file.

**Architecture:** The new file is assembled by reading the original `output/ing_investment_landscape.html` and injecting a landing `<section>` (plus CSS and JS) at precise positions. The shared `<header>` is reused verbatim. The dashboard content (`<nav>` through `<footer>`) is wrapped in `<div id="dashboard">`. Landing JS runs after dashboard JS so it can read the `DATA` and `FLAGS` consts.

**Tech Stack:** Vanilla HTML/CSS/JS. No new dependencies. Reuses Bricolage Grotesque, JetBrains Mono, Outfit from the original Google Fonts link. Python 3 used for file assembly.

---

## File Map

| File | Action | Purpose |
|------|--------|---------|
| `output/ing_investment_landscape.html` | Read-only | Source of all dashboard content |
| `output/ing_investment_landscape_with_landingpage.html` | Create | New integrated output |

---

## Task 1: Assemble the integrated file using Python

**Files:**
- Read: `output/ing_investment_landscape.html`
- Create: `output/ing_investment_landscape_with_landingpage.html`

This task reads the original, splits it at the correct points, and writes the integrated file with placeholder markers where CSS, HTML, and JS will be injected in later tasks.

- [ ] **Step 1: Verify original file is untouched and identify split points**

Run this to confirm line counts and key boundaries:
```bash
cd "C:/Users/SARP/Desktop/Devs/Investments analysis"
python3 - <<'EOF'
with open('output/ing_investment_landscape.html', encoding='utf-8') as f:
    lines = f.readlines()
print(f"Total lines: {len(lines)}")
# Find </style></head>, <body>, <nav class, </script>\n</body>
for i, l in enumerate(lines, 1):
    stripped = l.strip()
    if stripped == '</style>' and i < 970:
        print(f"Line {i}: </style> (end of CSS)")
    if stripped == '<body>':
        print(f"Line {i}: <body>")
    if 'class="country-nav"' in stripped:
        print(f"Line {i}: country-nav (dashboard start)")
    if stripped == '</script>' and i > 2200:
        print(f"Line {i}: </script> (end of dashboard JS)")
EOF
```

Expected output (approximate):
```
Total lines: 2270
Line 963: </style> (end of CSS)
Line 965: <body>
Line 986: country-nav (dashboard start)
Line 2268: </script> (end of dashboard JS)
```

- [ ] **Step 2: Write the assembly script**

```python
# Save as: assemble.py (run from project root, then delete)
with open('output/ing_investment_landscape.html', encoding='utf-8') as f:
    src = f.read()

# Split points (content-based, not line-number-based — robust to minor edits)
# 1. After </style></head> — inject landing CSS block
# 2. After </header> — inject landing section
# 3. Before <nav class="country-nav"> — open dashboard wrapper
# 4. Before </body> at the very end — inject landing JS + close dashboard wrapper

import re

# Marker 1: after closing </style></head>
# The original has: </style>\n</head>\n<body>
# We insert a second <style> block between </head> and <body>
assert '</style>\n</head>\n<body>' in src, "Marker 1 not found"

# Marker 2: after the </header> block (before <nav class="country-nav">)
assert '</header>\n\n<nav class="country-nav">' in src, "Marker 2 not found"

# Marker 3: before </body> at end of file
assert '\n</body>\n</html>' in src, "Marker 3 not found"

# --- Build new file ---

# Part A: everything up to and including </head>
part_a, rest = src.split('<body>', 1)  # part_a ends with </head>\n
part_a = part_a.rstrip()

# Part B: header only (from <body> to end of </header>)
header_end = rest.index('</header>') + len('</header>')
header_html = rest[:header_end]  # includes <body>\n\n<header...>...</header>

# Part C: nav through footer (from \n\n<nav to just before \n<script>)
after_header = rest[header_end:]
script_start = after_header.index('\n<script>')
nav_to_footer = after_header[:script_start]

# Part D: original script block (from <script> to </script>)
script_block = after_header[script_start:after_header.index('</script>') + len('</script>')]

integrated = (
    part_a + '\n' +
    '<!-- LANDING_CSS_PLACEHOLDER -->\n' +
    header_html + '\n' +
    '<!-- LANDING_HTML_PLACEHOLDER -->\n' +
    '<div id="dashboard">\n' +
    nav_to_footer +
    '\n</div><!-- /#dashboard -->\n' +
    script_block + '\n' +
    '<!-- LANDING_JS_PLACEHOLDER -->\n' +
    '</body>\n</html>\n'
)

with open('output/ing_investment_landscape_with_landingpage.html', 'w', encoding='utf-8') as f:
    f.write(integrated)

print("Done. Placeholders inserted:")
print(f"  LANDING_CSS_PLACEHOLDER: {'<!-- LANDING_CSS_PLACEHOLDER -->' in integrated}")
print(f"  LANDING_HTML_PLACEHOLDER: {'<!-- LANDING_HTML_PLACEHOLDER -->' in integrated}")
print(f"  LANDING_JS_PLACEHOLDER: {'<!-- LANDING_JS_PLACEHOLDER -->' in integrated}")
```

- [ ] **Step 3: Run the assembly script**

```bash
cd "C:/Users/SARP/Desktop/Devs/Investments analysis"
python3 assemble.py
```

Expected:
```
Done. Placeholders inserted:
  LANDING_CSS_PLACEHOLDER: True
  LANDING_HTML_PLACEHOLDER: True
  LANDING_JS_PLACEHOLDER: True
```

- [ ] **Step 4: Verify dashboard still renders (sanity check)**

Open `output/ing_investment_landscape_with_landingpage.html` in a browser. The header and dashboard content should be visible. Three `<!-- PLACEHOLDER -->` HTML comment markers are visible in page source but don't break rendering. The page looks identical to original minus the landing section.

- [ ] **Step 5: Delete assembly script**

```bash
rm assemble.py
```

- [ ] **Step 6: Commit**

```bash
cd "C:/Users/SARP/Desktop/Devs/Investments analysis"
git add output/ing_investment_landscape_with_landingpage.html
git commit -m "feat: scaffold integrated landing page file (placeholders)"
```

---

## Task 2: Inject landing page CSS

**Files:**
- Modify: `output/ing_investment_landscape_with_landingpage.html` (replace `LANDING_CSS_PLACEHOLDER`)

- [ ] **Step 1: Replace the CSS placeholder with the full landing styles**

Use the Edit tool to replace `<!-- LANDING_CSS_PLACEHOLDER -->` with the following (exact replacement, no whitespace changes around it):

```html
<style>
/* ═══════════════════════════════════════════════
   LANDING PAGE  —  .lp-* namespace
   All --variables inherited from dashboard :root
═══════════════════════════════════════════════ */

/* Section shell */
.lp-section{
  min-height:calc(100vh - 84px);
  display:flex;align-items:center;
  padding:0 56px;
  position:relative;overflow:hidden;
  border-bottom:1px solid var(--border);
}

/* Two-column grid */
.lp-inner{
  max-width:1580px;margin:0 auto;width:100%;
  display:grid;grid-template-columns:55fr 45fr;
  gap:80px;align-items:center;
  min-height:calc(100vh - 84px);
  padding:80px 0;
}

/* LEFT COLUMN */
.lp-left{display:flex;flex-direction:column;gap:28px}

.lp-eyebrow{
  font-family:var(--fm);font-size:10px;color:var(--t3);
  letter-spacing:0.16em;text-transform:uppercase;
}

.lp-headline{
  font-family:var(--fd);
  font-size:clamp(48px,5vw,76px);font-weight:800;
  color:var(--t1);letter-spacing:-2.5px;line-height:1.0;
  margin:0;
}
.lp-headline span{display:block}

.lp-subhead{
  font-family:var(--fb);font-size:16px;color:var(--t2);
  max-width:480px;line-height:1.75;
}

/* Glowing rule — animates scaleX 0→1 */
.lp-rule{
  height:1px;
  background:linear-gradient(90deg,var(--orange),#FF9500 50%,transparent);
  transform-origin:left;transform:scaleX(0);
  box-shadow:0 0 8px rgba(255,85,0,0.45);
  border-radius:1px;
}

/* Stat pills row */
.lp-stats{display:flex;align-items:center;gap:14px;flex-wrap:wrap}
.lp-stat{
  font-family:var(--fm);font-size:10px;color:var(--t3);
  letter-spacing:0.12em;text-transform:uppercase;
}
.lp-stat-dot{
  width:4px;height:4px;border-radius:50%;
  background:var(--orange);flex-shrink:0;opacity:0.7;
}

/* CTA row */
.lp-cta-row{display:flex;align-items:center;gap:22px;margin-top:8px}

.lp-btn{
  display:inline-flex;align-items:center;gap:10px;
  font-family:var(--fb);font-size:14px;font-weight:600;color:#fff;
  background:var(--orange);border:none;border-radius:10px;
  padding:14px 28px;cursor:pointer;letter-spacing:0.02em;
  box-shadow:0 0 28px rgba(255,85,0,0.4),0 0 56px rgba(255,85,0,0.14);
  transition:all 220ms var(--ease);position:relative;overflow:hidden;
}
.lp-btn::before{
  content:'';position:absolute;inset:0;
  background:linear-gradient(135deg,rgba(255,180,80,0.28) 0%,transparent 55%);
  pointer-events:none;
}
.lp-btn:hover{
  transform:translateY(-2px);
  box-shadow:0 8px 40px rgba(255,85,0,0.55),0 0 80px rgba(255,85,0,0.18);
}

/* Scroll hint */
.lp-scroll-hint{
  display:flex;flex-direction:column;align-items:center;gap:5px;
  font-family:var(--fm);font-size:9px;color:var(--t4);
  letter-spacing:0.1em;text-transform:uppercase;
  cursor:pointer;transition:color 200ms;
  background:none;border:none;
}
.lp-scroll-hint:hover{color:var(--t3)}
.lp-chevron{
  width:14px;height:14px;
  border-right:1.5px solid currentColor;border-bottom:1.5px solid currentColor;
  transform:rotate(45deg);
  animation:lpChevronBounce 1.8s ease-in-out infinite;
}
@keyframes lpChevronBounce{
  0%,100%{transform:rotate(45deg) translateY(0);opacity:0.45}
  50%{transform:rotate(45deg) translateY(5px);opacity:1}
}

/* RIGHT COLUMN */
.lp-right{
  display:flex;flex-direction:column;gap:14px;
  position:relative;
}
/* Orange atmospheric bloom */
.lp-right::before{
  content:'';position:absolute;
  top:50%;left:50%;transform:translate(-50%,-50%);
  width:520px;height:520px;
  background:radial-gradient(circle,rgba(255,85,0,0.055) 0%,transparent 65%);
  pointer-events:none;z-index:0;
}

/* Abstract chart */
.lp-chart-wrap{
  position:relative;z-index:1;
  height:88px;
  border:1px solid var(--border);border-radius:var(--r12);
  background:linear-gradient(145deg,var(--bg2),var(--bg3));
  overflow:hidden;
}
.lp-chart-label{
  position:absolute;top:11px;left:16px;
  font-family:var(--fm);font-size:8px;color:var(--t4);
  letter-spacing:0.12em;text-transform:uppercase;
}
.lp-chart-svg{
  position:absolute;bottom:0;left:0;right:0;height:70px;
  width:100%;
}

/* Country metric grid */
.lp-country-grid{
  display:grid;grid-template-columns:1fr 1fr;
  gap:10px;position:relative;z-index:1;
}

.lp-country-tile{
  background:linear-gradient(145deg,var(--bg2),var(--bg3));
  border:1px solid var(--border);border-radius:var(--r12);
  padding:16px 18px;position:relative;overflow:hidden;
  transition:border-color 220ms var(--ease),box-shadow 220ms var(--ease);
}
.lp-country-tile::before{
  content:'';position:absolute;top:0;left:0;right:0;height:2px;
  background:linear-gradient(90deg,var(--orange),#FF9500);
  opacity:0.45;
}
.lp-country-tile:hover{
  border-color:rgba(255,85,0,0.28);
  box-shadow:0 0 24px rgba(255,85,0,0.07);
}

.lp-tile-top{display:flex;align-items:center;gap:8px;margin-bottom:10px}
.lp-tile-flag{display:inline-flex;width:22px;height:15px;flex-shrink:0}
.lp-tile-flag svg{width:100%;height:100%}
.lp-tile-name{font-family:var(--fb);font-size:12px;font-weight:600;color:var(--t2)}
.lp-tile-label{
  font-family:var(--fm);font-size:8px;color:var(--t4);
  letter-spacing:0.1em;text-transform:uppercase;margin-bottom:4px;
}
.lp-tile-value{
  font-family:var(--fm);font-size:22px;color:var(--t1);
  letter-spacing:-0.5px;line-height:1;
}
.lp-tile-unit{
  font-family:var(--fm);font-size:11px;color:var(--t3);margin-left:1px;
}

/* Staggered reveal */
.lp-reveal{
  opacity:0;transform:translateY(14px);
  animation:lpReveal 580ms var(--ease) both;
}
@keyframes lpReveal{
  to{opacity:1;transform:translateY(0)}
}

/* Glowing rule scaleX reveal */
.lp-rule-reveal{
  animation:lpRule 600ms var(--ease) both;
}
@keyframes lpRule{
  to{transform:scaleX(1)}
}

/* Responsive: stack columns on narrow viewports */
@media(max-width:900px){
  .lp-inner{grid-template-columns:1fr;padding:60px 0;gap:40px}
  .lp-right{display:none}
  .lp-headline{font-size:48px;letter-spacing:-1.5px}
  .lp-section{padding:0 28px}
}
@media(max-width:480px){
  .lp-headline{font-size:36px}
  .lp-subhead{font-size:14px}
}
</style>
```

- [ ] **Step 2: Verify the placeholder was replaced**

```bash
grep -c "LANDING_CSS_PLACEHOLDER" "output/ing_investment_landscape_with_landingpage.html"
```
Expected: `0`

```bash
grep -c "lp-section" "output/ing_investment_landscape_with_landingpage.html"
```
Expected: `1` or more

- [ ] **Step 3: Commit**

```bash
git add output/ing_investment_landscape_with_landingpage.html
git commit -m "feat: add landing page CSS (.lp-* namespace)"
```

---

## Task 3: Inject landing page HTML

**Files:**
- Modify: `output/ing_investment_landscape_with_landingpage.html` (replace `LANDING_HTML_PLACEHOLDER`)

- [ ] **Step 1: Replace the HTML placeholder**

Use the Edit tool to replace `<!-- LANDING_HTML_PLACEHOLDER -->` with the following:

```html
<!-- ══ LANDING PAGE ══ -->
<section class="lp-section" id="landing" aria-label="Dashboard introduction">
  <div class="lp-inner">

    <!-- LEFT COLUMN -->
    <div class="lp-left">
      <div class="lp-eyebrow lp-reveal" style="animation-delay:150ms" aria-hidden="true">
        ING Group&nbsp;·&nbsp;FY2025&nbsp;·&nbsp;6 Markets
      </div>

      <h1 class="lp-headline" aria-label="European Investment Landscape Analysis">
        <span class="lp-reveal" style="animation-delay:300ms">European Investment</span>
        <span class="lp-reveal" style="animation-delay:450ms">Landscape Analysis</span>
      </h1>

      <p class="lp-subhead lp-reveal" style="animation-delay:560ms">
        A source-verified competitive benchmark across six European markets.
        Central bank household data. FY2025 figures throughout.
      </p>

      <div class="lp-rule lp-rule-reveal" style="animation-delay:900ms" aria-hidden="true"></div>

      <div class="lp-stats lp-reveal" style="animation-delay:1060ms" aria-label="Key metrics">
        <span class="lp-stat">6 European Markets</span>
        <span class="lp-stat-dot" aria-hidden="true"></span>
        <span class="lp-stat">Source-Verified Data</span>
        <span class="lp-stat-dot" aria-hidden="true"></span>
        <span class="lp-stat">FY2025 Figures</span>
      </div>

      <div class="lp-cta-row lp-reveal" style="animation-delay:1120ms">
        <button class="lp-btn" id="lp-enter-btn" aria-label="Enter the analysis dashboard">
          Enter Analysis
          <svg width="16" height="16" viewBox="0 0 16 16" fill="none" aria-hidden="true">
            <path d="M3 8h10M9 4l4 4-4 4" stroke="currentColor" stroke-width="1.5"
                  stroke-linecap="round" stroke-linejoin="round"/>
          </svg>
        </button>
        <button class="lp-scroll-hint" id="lp-scroll-btn" aria-label="Scroll to dashboard">
          <div class="lp-chevron" aria-hidden="true"></div>
          <span>Scroll</span>
        </button>
      </div>
    </div>

    <!-- RIGHT COLUMN — populated by landing JS -->
    <div class="lp-right lp-reveal" style="animation-delay:220ms" aria-hidden="true">
      <div class="lp-chart-wrap">
        <span class="lp-chart-label">Market Trajectory</span>
        <svg class="lp-chart-svg" viewBox="0 0 600 70" preserveAspectRatio="none" fill="none"
             aria-hidden="true">
          <path id="lp-path1"
            d="M0,60 C60,57 120,50 190,42 C260,34 310,24 370,18 C430,12 490,8 600,5"
            stroke="rgba(255,85,0,0.45)" stroke-width="1.5"/>
          <path id="lp-path2"
            d="M0,65 C70,62 140,56 210,50 C280,44 340,36 410,28 C480,20 540,13 600,9"
            stroke="rgba(0,212,255,0.28)" stroke-width="1"/>
        </svg>
      </div>
      <div class="lp-country-grid" id="lp-country-grid">
        <!-- Tiles injected by lpInit() in landing JS -->
      </div>
    </div>

  </div>
</section>
<!-- ══ END LANDING PAGE ══ -->
```

- [ ] **Step 2: Verify placeholder replaced**

```bash
grep -c "LANDING_HTML_PLACEHOLDER" "output/ing_investment_landscape_with_landingpage.html"
```
Expected: `0`

```bash
grep -c "lp-enter-btn" "output/ing_investment_landscape_with_landingpage.html"
```
Expected: `1`

- [ ] **Step 3: Open file in browser — verify landing section is visible**

The page should now show: shared header → landing section (text visible, right column empty, no animations yet) → dashboard below the fold.

- [ ] **Step 4: Commit**

```bash
git add output/ing_investment_landscape_with_landingpage.html
git commit -m "feat: add landing page HTML section"
```

---

## Task 4: Inject landing page JavaScript

**Files:**
- Modify: `output/ing_investment_landscape_with_landingpage.html` (replace `LANDING_JS_PLACEHOLDER`)

The landing JS must run AFTER the dashboard's `<script>` block because it reads `DATA` and `FLAGS` which are defined there.

- [ ] **Step 1: Replace the JS placeholder**

Use the Edit tool to replace `<!-- LANDING_JS_PLACEHOLDER -->` with the following:

```html
<script>
// ══════════════════════════════════════════
//  LANDING PAGE — runs after dashboard JS
//  Reads DATA and FLAGS from dashboard scope
// ══════════════════════════════════════════
(function lpInit() {

  // ── Count-up animation ──────────────────
  function countUp(el, target, durationMs) {
    const start = performance.now();
    function tick(now) {
      const p = Math.min((now - start) / durationMs, 1);
      const eased = 1 - Math.pow(1 - p, 3); // cubic ease-out
      const v = target * eased;
      el.textContent = v >= 1000
        ? (v / 1000).toFixed(1) + 'k'
        : Math.round(v).toLocaleString('en-US');
      if (p < 1) requestAnimationFrame(tick);
      else el.textContent = target >= 1000
        ? (target / 1000).toFixed(1) + 'k'
        : Math.round(target).toLocaleString('en-US');
    }
    requestAnimationFrame(tick);
  }

  // ── Build 6 country tiles ───────────────
  function buildTiles() {
    const grid = document.getElementById('lp-country-grid');
    if (!grid || typeof DATA === 'undefined' || typeof FLAGS === 'undefined') return;

    DATA.forEach(function(c, i) {
      const inv = c.market.inv.v;
      const unit = inv >= 1000 ? 'tn' : 'bn';
      const displayVal = inv >= 1000 ? (inv / 1000).toFixed(1) : Math.round(inv);

      const tile = document.createElement('div');
      tile.className = 'lp-country-tile';

      tile.innerHTML =
        '<div class="lp-tile-top">' +
          '<span class="lp-tile-flag">' + FLAGS[c.code] + '</span>' +
          '<span class="lp-tile-name">' + c.name + '</span>' +
        '</div>' +
        '<div class="lp-tile-label">Total Market</div>' +
        '<div class="lp-tile-value">' +
          '<span class="lp-count-' + c.code + '">0</span>' +
          '<span class="lp-tile-unit">&thinsp;' + unit + '</span>' +
        '</div>';

      grid.appendChild(tile);

      // Stagger count-up start
      var delay = 620 + i * 90;
      setTimeout(function() {
        var numEl = tile.querySelector('.lp-count-' + c.code);
        if (numEl) countUp(numEl, inv >= 1000 ? inv / 1000 : inv, 1100);
      }, delay);
    });
  }

  // ── Animate SVG chart paths ─────────────
  function animateChart() {
    [['lp-path1', 680], ['lp-path2', 820]].forEach(function(pair) {
      var el = document.getElementById(pair[0]);
      var delay = pair[1];
      if (!el) return;
      var len = el.getTotalLength();
      el.style.strokeDasharray = len;
      el.style.strokeDashoffset = len;
      setTimeout(function() {
        el.style.transition = 'stroke-dashoffset 900ms cubic-bezier(0.4,0,0.2,1)';
        el.style.strokeDashoffset = '0';
      }, delay);
    });
  }

  // ── CTA scroll handlers ─────────────────
  function wireScrollCTA() {
    function scrollToDashboard() {
      var target = document.getElementById('dashboard');
      if (target) target.scrollIntoView({ behavior: 'smooth' });
    }
    var enterBtn = document.getElementById('lp-enter-btn');
    var scrollBtn = document.getElementById('lp-scroll-btn');
    if (enterBtn) enterBtn.addEventListener('click', scrollToDashboard);
    if (scrollBtn) scrollBtn.addEventListener('click', scrollToDashboard);
  }

  // ── Init ────────────────────────────────
  if (document.readyState === 'loading') {
    document.addEventListener('DOMContentLoaded', function() {
      buildTiles();
      animateChart();
      wireScrollCTA();
    });
  } else {
    // DOMContentLoaded already fired
    buildTiles();
    animateChart();
    wireScrollCTA();
  }

}());
</script>
```

- [ ] **Step 2: Verify placeholder replaced**

```bash
grep -c "LANDING_JS_PLACEHOLDER" "output/ing_investment_landscape_with_landingpage.html"
```
Expected: `0`

```bash
grep -c "lpInit" "output/ing_investment_landscape_with_landingpage.html"
```
Expected: `1`

- [ ] **Step 3: Verify no remaining placeholders**

```bash
grep -n "PLACEHOLDER" "output/ing_investment_landscape_with_landingpage.html"
```
Expected: no output (0 matches).

- [ ] **Step 4: Commit**

```bash
git add output/ing_investment_landscape_with_landingpage.html
git commit -m "feat: add landing page JavaScript (count-up, chart, CTA)"
```

---

## Task 5: End-to-end verification

**Files:**
- Read: `output/ing_investment_landscape_with_landingpage.html`

- [ ] **Step 1: Open the file in the default browser**

```bash
start "" "output/ing_investment_landscape_with_landingpage.html"
```

- [ ] **Step 2: Verify landing page checklist**

Confirm each item visually:
- [ ] Shared header visible at top (ING logo, "Confidential" badge, date badge)
- [ ] Eyebrow text appears: "ING GROUP · FY2025 · 6 MARKETS"
- [ ] Headline animates in two lines: "European Investment" / "Landscape Analysis"
- [ ] Subhead text visible below headline
- [ ] Orange glowing rule sweeps left-to-right
- [ ] Three stat pills appear: "6 European Markets · Source-Verified Data · FY2025 Figures"
- [ ] "Enter Analysis →" button glows orange
- [ ] Right column: 2×3 grid of country tiles with count-up numbers
- [ ] SVG line chart animates (two paths draw in)
- [ ] Scroll chevron pulses

- [ ] **Step 3: Verify CTA transition**

Click "Enter Analysis →" — page should smooth-scroll to the dashboard section (country nav becomes visible).

- [ ] **Step 4: Verify all dashboard functionality**

After scrolling to dashboard, check:
- [ ] Country tabs (DE/BE/ES/IT/NL/PL) all switch views correctly
- [ ] 📐 Methodology tab shows methodology content
- [ ] ⚠ Data Quality tab shows caveats
- [ ] 📄 Sources tab shows sources
- [ ] Market cards have count-up animations on first load
- [ ] Competitor table is sortable (click column headers)
- [ ] Bar charts animate when a country deep-dive loads
- [ ] Market penetration section expands/collapses
- [ ] "Explore →" buttons on market cards open deep-dive for that country

- [ ] **Step 5: Verify original file untouched**

```bash
git diff HEAD -- output/ing_investment_landscape.html
```
Expected: no output (file unchanged since baseline).

- [ ] **Step 6: Final commit**

```bash
git add output/ing_investment_landscape_with_landingpage.html
git commit -m "feat: complete landing page integration — instrument panel aesthetic"
```

---

## Self-Review Checklist

**Spec coverage:**
- ✅ Landing page opens first (lp-section is above #dashboard)
- ✅ "Instrument Panel" aesthetic: data canvas, count-up metrics, animated chart
- ✅ Typography: Bricolage Grotesque + JetBrains Mono + Outfit (reused from dashboard)
- ✅ Color: navy + ING orange + cyan (exact dashboard variables)
- ✅ Asymmetric two-column layout
- ✅ Staggered animation sequence (150ms → 1120ms)
- ✅ Count-up numbers read from DATA const
- ✅ SVG line chart stroke-dashoffset animation
- ✅ Glowing rule scaleX reveal
- ✅ CTA button + scroll chevron → smooth scroll to #dashboard
- ✅ Stat pills (3 × credibility stats)
- ✅ Original file never modified (read-only)
- ✅ Dashboard functionality: all tabs, sorts, charts, animations preserved
- ✅ Self-contained: no new external dependencies
- ✅ Responsive: right column hides at ≤900px

**Type consistency:** `DATA[n].market.inv.v` used consistently in Task 4 JS. `FLAGS[c.code]` used consistently. `lp-count-${c.code}` selector generated and queried consistently.

**No placeholders:** All steps contain complete, runnable code.
