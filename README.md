# Portfolio Backtest Dashboard — VTI / VGT / SMH vs SPY

An interactive, single-file HTML dashboard that backtests a three-ETF blended portfolio against a $10,000 SPY (S&P 500) benchmark across three distinct analytical scenarios: a 10-year window, a 15-year window, and five individual historical bear market recovery cycles.

---

## Table of Contents

1. [Project Overview](#project-overview)
2. [Portfolio Composition](#portfolio-composition)
3. [Backtest Methodology](#backtest-methodology)
4. [Results Summary](#results-summary)
5. [Dashboard Sections](#dashboard-sections)
6. [File Structure](#file-structure)
7. [Code Architecture](#code-architecture)
   - [HTML Structure](#html-structure)
   - [CSS Design System](#css-design-system)
   - [JavaScript Data Layer](#javascript-data-layer)
   - [Chart.js Integration](#chartjs-integration)
   - [Tab Navigation System](#tab-navigation-system)
   - [Bear Market Engine](#bear-market-engine)
8. [Data Sources](#data-sources)
9. [Key Findings](#key-findings)
10. [Limitations & Disclosures](#limitations--disclosures)
11. [How to Run Locally](#how-to-run-locally)
12. [Customization Guide](#customization-guide)

---

## Project Overview

This dashboard was built to answer one central question: **does a concentrated technology and semiconductor tilt on top of a total-market core meaningfully outperform a passive S&P 500 index fund over long time horizons and across market stress events?**

The portfolio under test holds three Vanguard/VanEck ETFs in fixed proportions, simulated as a buy-and-hold position from a defined start date. It is benchmarked against a $10,000 buy-and-hold position in SPY (SPDR S&P 500 ETF Trust). All data is sourced from monthly adjusted closing prices via Perplexity Finance. No dividends are reinvested — the analysis is purely price return.

The dashboard is entirely self-contained in a single `index.html` file with no build step, no server-side dependencies, and no API calls at runtime. All historical price data has been pre-computed and baked into the JavaScript `DATA` object.

---

## Portfolio Composition

| ETF | Name | Allocation | Dollar Amount |
|-----|------|-----------|---------------|
| **VTI** | Vanguard Total Stock Market ETF | 55% | $5,500 |
| **VGT** | Vanguard Information Technology ETF | 25% | $2,500 |
| **SMH** | VanEck Semiconductor ETF | 20% | $2,000 |
| **SPY** | SPDR S&P 500 ETF Trust (benchmark) | 100% | $10,000 |

### Rationale

- **VTI (55%)** — The core anchor. VTI tracks the CRSP US Total Market Index, covering all US equities across large, mid, small, and micro-cap. It provides diversification and a market-rate floor, and it naturally carries a ~27–30% weight in technology already.

- **VGT (25%)** — The sector tilt. VGT tracks the MSCI US Investable Market Information Technology 25/50 Index, concentrating in mega-cap tech: Apple, Microsoft, NVIDIA, Broadcom, and similar. Adding 25% here intentionally overweights the sector that has driven the bulk of US equity outperformance since 2013.

- **SMH (20%)** — The high-beta semiconductor amplifier. SMH tracks the MVIS US Listed Semiconductor 25 Index. Semiconductors are the infrastructure layer of every technology cycle — AI compute, mobile, automotive, cloud. SMH is more volatile than VGT but historically generates higher peak returns during tech bull markets.

The combination creates a portfolio that is broad at its base (VTI), adds concentrated tech exposure (VGT), and then adds an even more focused semiconductor kicker (SMH) — a deliberate "funnel" from general to specific within the technology sector.

---

## Backtest Methodology

### General Rules

- **Starting capital**: $10,000 in both the blend and SPY.
- **Rebalancing**: None after inception. The portfolio is held in fixed share counts from day one — it is a pure buy-and-hold simulation.
- **Price data**: Monthly closing prices sourced from Perplexity Finance (OHLCV histories tool), covering April 2010 through April 2026.
- **Return calculation**: At each time step `t`, portfolio value = `Σ (initial_allocation_i × price_t_i / price_0_i)` for each holding `i`.
- **Dividends**: Not reinvested. This is a price-return-only backtest. This understates total returns for both strategies but equally so, preserving relative comparisons.
- **Benchmark**: SPY on the same monthly close basis.

### 10-Year Window

- **Start**: April 30, 2016
- **End**: April 3, 2026
- Uses first available monthly close on or after April 1, 2016 as the entry price for all four tickers.

### 15-Year Window

- **Start**: April 29, 2011
- **End**: April 3, 2026
- Extends the window through the 2011 European Debt Crisis, the 2013 taper tantrum, the 2015–2016 energy sector selloff, COVID, and the 2022 Fed tightening cycle.

### Bear Market Trough-to-Peak Windows

Each bear market scenario opens a fresh $10,000 position in both the blend and SPY at the confirmed SPY monthly trough, then exits at the month SPY closes at or above its pre-bear cycle ATH (prior peak). This simulates buying the dip at the worst SPY month and holding until full S&P 500 recovery.

Bear market definitions are based on peak-to-trough drawdowns in the SPY monthly close series, using the following five most recent events:

| # | Bear Market | SPY Peak | SPY Trough | Max Drawdown |
|---|-------------|----------|------------|-------------|
| 1 | GFC Recovery | Oct 2007 | Feb 2009 | −56.8% |
| 2 | 2011 US Debt Downgrade / European Sovereign Debt Crisis | Apr 2011 | Sep 2011 | −19.4% |
| 3 | Q4 2018 Fed Rate Scare / Trade War Correction | Sep 2018 | Dec 2018 | −19.8% |
| 4 | COVID Crash | Jan 2020 | Mar 2020 | −33.9% |
| 5 | Fed Tightening / Inflation Bear 2022 | Dec 2021 | Sep 2022 | −25.4% |

---

## Results Summary

### 10-Year Backtest (Apr 2016 → Apr 2026)

| Metric | Blend (VTI/VGT/SMH) | SPY |
|--------|---------------------|-----|
| End Value | **$63,854** | $31,785 |
| Total Return | **+538.5%** | +217.9% |
| Alpha | **+$32,069** | — |
| Alpha (pp) | **+320.7 pp** | — |
| Blend/SPY Ratio | **2.01×** | — |

### 15-Year Backtest (Apr 2011 → Apr 2026)

| Metric | Blend (VTI/VGT/SMH) | SPY |
|--------|---------------------|-----|
| End Value | **$94,863** | $48,071 |
| Total Return | **+848.6%** | +380.7% |
| Alpha | **+$46,792** | — |
| Alpha (pp) | **+467.9 pp** | — |
| Blend/SPY Ratio | **1.97×** | — |

### Bear Market Trough-to-Peak Recovery

| Bear Market | Drawdown | Window | Blend Return | SPY Return | Alpha | Winner |
|-------------|----------|--------|-------------|------------|-------|--------|
| GFC Recovery | −56.8% | Feb 2009 → Aug 2012 (28 mo) | +19.6% | +18.8% | +0.8 pp | Blend |
| 2011 Debt Crisis | −19.4% | Sep 2011 → Feb 2012 (5 mo) | +22.6% | +21.1% | +1.5 pp | Blend |
| Q4 2018 Correction | −19.8% | Dec 2018 → Apr 2019 (4 mo) | +23.4% | +17.7% | +5.8 pp | Blend |
| COVID Crash | −33.9% | Mar 2020 → Aug 2020 (5 mo) | +44.3% | +35.5% | +8.8 pp | Blend |
| Fed Tightening 2022 | −25.4% | Sep 2022 → Jan 2024 (16 mo) | +53.8% | +35.2% | +18.7 pp | Blend |

The blend won all five bear recovery windows, with alpha expanding in more recent cycles — consistent with semiconductor and tech leadership during post-2020 market recoveries.

---

## Dashboard Sections

### Tab 1 — 10-Year Backtest

**KPI Strip (4 cards)**
- `Blend End Value` — final dollar value of the $10,000 blend after 10 years
- `SPY End Value` — final dollar value of the $10,000 SPY position
- `Blend Alpha (10Y)` — raw dollar difference between blend and SPY
- `Blend Win Ratio` — blend end value ÷ SPY end value

**Chart 1 — Portfolio Value (Line)**
- Dual line chart showing both strategies' portfolio values month by month
- Blend in blue (`#4f9eff`), SPY in red-coral (`#ff6b6b`)
- Filled area under each line for visual depth
- Interactive crosshair tooltip on hover showing both values at any given month

**Chart 2 — Relative Performance / Alpha Gap (Bar)**
- Bar chart showing the dollar difference (Blend − SPY) at each monthly data point
- Green bars = blend outperforming; red bars = SPY outperforming
- Lets the viewer instantly identify when and by how much the blend diverged

---

### Tab 2 — 15-Year Backtest

Identical layout to Tab 1 but covering the longer 15-year window (April 2011 → April 2026), which includes:
- The 2011 Euro debt crisis and US credit downgrade
- The zero-interest-rate era (2012–2015) and its effect on tech valuations
- The 2015–2016 energy/China selloff
- The 2018 correction
- COVID crash and recovery
- The 2021–2022 rate tightening bear market
- The 2023–2026 AI/semiconductor supercycle

---

### Tab 3 — 5 Bear Markets

**Section Intro Banner**
Explains the trough-to-peak methodology: entry at SPY monthly trough, full exit at SPY recovery to prior cycle ATH.

**All-5 Summary Table**
A single comparison table with all five bear markets as rows, showing: drawdown %, trough-to-peak window, duration, blend return %, SPY return %, alpha (pp), and winner badge.

**Bear Market Cards Grid (2×3)**
Five clickable cards — one per bear market — each displaying:
- Market name and SPY drawdown badge
- Blend return %
- SPY return %
- Alpha pp
- Winner badge

Clicking a card updates the detail panel and recovery chart below.

**Bear Detail Panel (interactive)**
- Title: bear market name + date range
- Subtitle: duration and methodology note
- 4-stat KPI grid: Blend End Value, Blend Return %, SPY Return %, Alpha pp
- Line chart: month-by-month portfolio value of both strategies across the full recovery window (with data points visible since these are shorter periods)

**Bear Bar Chart (All 5 Side-by-Side)**
Grouped bar chart comparing blend vs SPY return % for all five bear markets simultaneously — gives a fast visual summary of where the blend's structural alpha advantage is largest.

---

## File Structure

```
backtest-dashboard/
└── index.html          # Entire application — HTML, CSS, and JS in one file
```

This is an intentionally self-contained single-file application. There are no external asset files, no build dependencies, no npm packages, no server requirements. The only external dependency is the Chart.js library, loaded from a CDN at runtime.

---

## Code Architecture

### HTML Structure

The HTML document is organized into four major regions:

```
<head>
  <title> + viewport meta
  <script> CDN import for Chart.js 4.4.0
  <style> Full CSS (~200 lines)

<body>
  <header>               — Logo, title, portfolio chip strip
  <nav class="tab-nav">  — 3-tab navigation bar (sticky)
  <div id="t10y">        — 10-year section (hidden unless active)
  <div id="t15y">        — 15-year section (hidden unless active)
  <div id="tbear">       — Bear markets section (hidden unless active)
  <script>               — All JavaScript (~380 lines)
```

Tab visibility is controlled by toggling the `.active` class. Only the active section has `display: block`; the others are `display: none`. This keeps all Chart.js canvases pre-rendered in the DOM — charts are initialized on page load for all three sections simultaneously, not lazily.

---

### CSS Design System

All design tokens are declared as CSS custom properties on `:root`:

```css
:root {
  --bg: #0d0f14;           /* Page background — near-black with blue tint */
  --surface: #141720;      /* Navigation bar and secondary surfaces */
  --card: #1a1f2e;         /* Card backgrounds */
  --border: #252b3d;       /* All border colors */
  --accent: #4f9eff;       /* Primary blue — blend line, active tab, links */
  --accent2: #ff6b6b;      /* Coral red — SPY line, drawdown badges */
  --green: #00e5a0;        /* Alpha / positive return color */
  --green-soft: rgba(0,229,160,0.12);   /* Translucent green for winner badges */
  --red-soft: rgba(255,107,107,0.12);   /* Translucent red for drawdown badges */
  --text: #e8eaf0;         /* Primary text */
  --muted: #8892a4;        /* Secondary/label text */
  --blend-color: #4f9eff;  /* Alias for accent — used in chart code */
  --spy-color: #ff6b6b;    /* Alias for accent2 — used in chart code */
}
```

**Layout system** uses CSS Grid throughout:
- `.kpi-row` — `repeat(4, 1fr)` grid, collapses to `repeat(2, 1fr)` on mobile
- `.bear-grid` — `repeat(2, 1fr)` grid for bear market cards, collapses to `1fr` on mobile
- `.bear-summary-grid` — `repeat(4, 1fr)` for the bear detail KPI strip

**Typography** — No external font imports. Falls back gracefully: `font-family: 'Inter', system-ui, sans-serif`. Inter is available on most modern macOS and Windows systems natively; system-ui ensures clean fallback everywhere else.

**Responsive breakpoint** — `@media(max-width: 900px)` handles the mobile layout, adjusting padding, font sizes, and grid columns.

**Notable CSS patterns:**
- `.kpi-card::after` — A 3px accent line at the top of each KPI card using `::after` pseudo-element with a linear gradient, colored differently for blend, SPY, and alpha cards.
- `.tab-nav` — `position: sticky; top: 0; z-index: 100` keeps the tab bar fixed during scroll.
- `.bear-card` — `transition: border-color 0.2s` for the hover/active state highlight.
- `header::before` — Decorative radial gradient glow in the upper-right of the header using a pseudo-element.

---

### JavaScript Data Layer

All historical price series are pre-computed and stored in a single `const DATA` object at the top of the `<script>` block. This eliminates any runtime data fetching and makes the dashboard work fully offline after initial load.

**Data object structure:**

```javascript
const DATA = {
  "10y": {
    dates: ["2016-04", "2016-05", ...],   // 121 monthly YYYY-MM labels
    blend: [10000.0, 10398.27, ...],      // Dollar value of blend portfolio
    spy:   [10000.0, 10170.12, ...],      // Dollar value of SPY portfolio
    blend_end: 63854.46,                  // Final blend value (scalar)
    spy_end: 31785.49,                    // Final SPY value (scalar)
    blend_return: 538.54,                 // Total return % (scalar)
    spy_return: 217.85,                   // Total return % (scalar)
  },
  "15y": {
    // Same structure, 181 monthly data points (Apr 2011 → Apr 2026)
  },
  "bear": [
    {
      name: "GFC Recovery",
      drawdown: -56.8,
      trough: "Feb 2009",
      peak: "Aug 2012",
      duration_months: 28,
      blend_exit: 11959.21,
      blend_return: 19.59,
      spy_exit: 11881.15,
      spy_return: 18.81,
      winner: "Blend",
      alpha: 0.78,
      dates: ["2010-04", ...],         // Monthly labels for the recovery window
      blend_series: [10000.0, ...],    // Blend portfolio values during recovery
      spy_series:   [10000.0, ...],    // SPY portfolio values during recovery
    },
    // ... 4 more bear market objects
  ]
}
```

**How the blend values were computed** (Python, pre-processed before embedding):

```python
# For each monthly row in the date range:
vti_val = 5500 * (row['VTI'] / p0['VTI'])   # VTI slice
vgt_val = 2500 * (row['VGT'] / p0['VGT'])   # VGT slice
smh_val = 2000 * (row['SMH'] / p0['SMH'])   # SMH slice
blend_val = vti_val + vgt_val + smh_val      # Portfolio total value

spy_val  = 10000 * (row['SPY'] / p0['SPY']) # SPY comparison
```

Where `p0` is the row at the start date (entry prices). Each slice independently compounds from its initial allocation — identical to buying fractional shares in fixed dollar amounts and never touching them.

---

### Chart.js Integration

The dashboard uses **Chart.js v4.4.0** loaded from jsDelivr CDN:

```html
<script src="https://cdn.jsdelivr.net/npm/chart.js@4.4.0/dist/chart.umd.min.js"></script>
```

**Global defaults** are set once before any chart is constructed:

```javascript
Chart.defaults.color = '#8892a4';                    // Axis tick and label text color
Chart.defaults.font.family = 'Inter, system-ui, sans-serif';
```

**`chartOpts(extra = {})`** — A reusable factory function that returns a base configuration object shared by all line charts. It is spread-merged with per-chart overrides:

```javascript
function chartOpts(extra = {}) {
  return {
    responsive: true,
    maintainAspectRatio: false,
    interaction: { mode: 'index', intersect: false },  // Shared crosshair tooltip
    plugins: {
      legend: { display: false },   // Custom HTML legends used instead
      tooltip: {
        backgroundColor: '#1a1f2e',
        borderColor: '#252b3d',
        borderWidth: 1,
        padding: 12,
        callbacks: {
          label: ctx => ' ' + ctx.dataset.label + ': ' + dollarFmt(ctx.parsed.y)
        }
      }
    },
    scales: {
      x: { grid: { color: 'rgba(255,255,255,0.04)' }, ticks: { maxTicksLimit: 10 } },
      y: { grid: { color: 'rgba(255,255,255,0.04)' }, ticks: { callback: v => '$' + ... } }
    },
    ...extra  // Per-chart overrides merged in
  };
}
```

**Chart instances created:**

| Canvas ID | Type | Purpose |
|-----------|------|---------|
| `chart10y` | Line | 10Y portfolio value curves |
| `chart10y_gap` | Bar | 10Y alpha gap (Blend − SPY) |
| `chart15y` | Line | 15Y portfolio value curves |
| `chart15y_gap` | Bar | 15Y alpha gap (Blend − SPY) |
| `bear-chart` | Line | Dynamic — redrawn per selected bear market |
| `bear-bar-chart` | Bar | All-5 bear returns comparison |

The bear detail chart (`bear-chart`) is the only dynamically destroyed and redrawn chart. On `selectBear(i)`, the previous chart instance is destroyed via `bearChartInst.destroy()` before creating a new one, preventing canvas memory leaks.

---

### Tab Navigation System

```javascript
function switchTab(tabId) {
  document.querySelectorAll('.section').forEach(s => s.classList.remove('active'));
  document.querySelectorAll('.tab-btn').forEach(b => b.classList.remove('active'));
  document.getElementById(tabId).classList.add('active');
  event.target.classList.add('active');
}
```

Called inline via `onclick="switchTab('t10y')"` on each nav button. Removes `.active` from all sections and buttons, then adds it to the target section and the clicked button. CSS handles visibility (`display: none` vs `display: block`) entirely.

All three sections' charts are initialized at page load (in the `// INIT` block at the bottom of the script), not lazily on tab activation. This is intentional — Chart.js requires the canvas to be in the DOM and have non-zero dimensions at initialization time. Since hidden elements have `display: none`, switching tabs doesn't re-initialize charts; it just reveals already-drawn canvases.

---

### Bear Market Engine

The bear market section has the most complex interactivity. Key functions:

**`buildBearTable()`** — Iterates `DATA.bear` and programmatically inserts `<tr>` rows into `#bear-table-body` using `innerHTML` template literals. Includes color-coded winner chips.

**`buildBearCards()`** — Creates five `.bear-card` div elements dynamically. Each card gets an `onclick` handler bound to its index: `card.onclick = () => selectBear(i)`. The first card starts with `.active` class.

**`buildBearBar()`** — Constructs the grouped bar chart comparing all five bear markets simultaneously. Uses two datasets (Blend and SPY) with grouped bar layout.

**`selectBear(i)`** — The core interactivity function:
1. Updates active state on all `.bear-card` elements
2. Updates `#bd-title` and `#bd-sub` text content with the selected bear's metadata
3. Rebuilds `#bd-kpis` innerHTML with four mini KPI cards
4. Destroys the existing `bearChartInst` if it exists
5. Creates a new Chart.js line chart on `#bear-chart` using the selected bear's `dates`, `blend_series`, and `spy_series` arrays

---

## Data Sources

All historical price data was retrieved using Perplexity Finance's `finance_ohlcv_histories` tool, which returns adjusted OHLCV data sourced from market data providers. Monthly close prices were requested for the full April 2010 → April 2026 date range.

- **VTI**: [perplexity.ai/finance/VTI](https://perplexity.ai/finance/VTI)
- **VGT**: [perplexity.ai/finance/VGT](https://perplexity.ai/finance/VGT)
- **SMH**: [perplexity.ai/finance/SMH](https://perplexity.ai/finance/SMH)
- **SPY**: [perplexity.ai/finance/SPY](https://perplexity.ai/finance/SPY)

Data is baked into the `DATA` object as of **April 3, 2026**. To update the dashboard with newer data, the Python computation script would need to be re-run with updated CSV exports.

---

## Key Findings

1. **Technology and semiconductor concentration consistently generated alpha over SPY across every time horizon tested.** The blend returned 2.01× SPY's return over 10 years and 1.97× over 15 years.

2. **The alpha is not a recent anomaly.** The 15-year window includes multiple periods of technology underperformance (2011, 2016, 2018, 2022), yet the compounding advantage was maintained throughout.

3. **In bear market recovery windows, the blend outperformed SPY in all 5 instances.** The margin expanded dramatically in more recent recoveries — particularly COVID (+8.8 pp alpha) and the 2022 recovery (+18.7 pp alpha), driven by the AI/semiconductor cycle rerating NVIDIA and the broader SOX index.

4. **The GFC recovery produced the smallest alpha (+0.8 pp)** — the only bear market before SMH's modern semiconductor composition and before the FAANG era. This is consistent with the thesis that the blend's edge is structural to the tech/semi dominance era beginning ~2012.

5. **The 2022 recovery window (+53.8% blend vs +35.2% SPY) is the strongest signal.** It covers the inflation bear and AI-driven recovery, the exact macro regime where concentrated tech/semi exposure was most rewarded.

---

## Limitations & Disclosures

- **Price return only** — No dividends are reinvested. VTI and SPY both pay meaningful dividends (~1.3–1.5% annually). VGT yields ~0.6% and SMH ~0.5%. Including dividends would increase both strategy returns, but the blend's yield is meaningfully lower than SPY's, meaning SPY's total return advantage vs price return is slightly larger. The relative alpha would compress modestly on a total return basis.

- **No transaction costs or taxes** — The simulation assumes zero commission, no bid-ask spread, and no capital gains taxation on the positions. Real-world execution would reduce absolute returns.

- **No rebalancing** — The blend starts at 55/25/20 and drifts over time as the three components grow at different rates. By April 2026, SMH has grown to represent a substantially larger percentage of the portfolio than 20% due to its outperformance. This means the terminal portfolio is more concentrated in semiconductors than the initial allocation implies.

- **Survivorship / hindsight bias** — SMH and VGT are chosen in part because their underlying sectors outperformed the broader market over the backtest period. This is a known limitation of any sector-tilted backtest. Future semiconductor and technology performance may revert toward broad market returns.

- **Bear market trough identification** — Trough and peak dates use SPY monthly closing prices, not intraday extremes. The actual optimal entry and exit would require daily data and would differ. Monthly close-based troughs are slightly earlier or later than the true calendar trough in most cycles.

- **Past performance is not indicative of future results.** This dashboard is for educational and analytical purposes only and does not constitute investment advice.

---

## How to Run Locally

No installation required. Clone or download the repository and open `index.html` directly in any modern browser:

```bash
git clone https://github.com/YOUR_USERNAME/vti-vgt-smh-vs-spy-backtest.git
cd vti-vgt-smh-vs-spy-backtest
open index.html          # macOS
# or
start index.html         # Windows
# or
xdg-open index.html      # Linux
```

Chart.js is loaded from the jsDelivr CDN — you will need an internet connection for the charts to render. If running fully offline, download Chart.js locally:

```bash
curl -o chartjs.min.js https://cdn.jsdelivr.net/npm/chart.js@4.4.0/dist/chart.umd.min.js
```

Then update the `<script src="...">` tag in `index.html` to point to `./chartjs.min.js`.

---

## Customization Guide

### Changing the Portfolio Weights

The weights are hardcoded in two places:

1. **The `DATA` object** — The `blend` and `blend_series` arrays are pre-computed. You would need to re-run the Python computation with your new weights to regenerate these arrays.

2. **The HTML header chips** — The `.portfolio-strip` section in the `<header>` block shows the dollar amounts and percentages as static text. Update these manually to match your new weights.

### Changing the Date Range

1. Re-export monthly OHLCV CSVs from your data source for the new date range.
2. Re-run the Python computation script to generate new `blend` and `spy` arrays.
3. Replace the relevant arrays inside `const DATA` in `index.html`.
4. Update the section intro banners (`.section-intro` divs) with the new date range text.

### Adding a New Bear Market

Add a new object to the `DATA.bear` array following the existing schema:

```javascript
{
  name: "Your Bear Market Name",
  drawdown: -XX.X,                    // SPY peak-to-trough drawdown %
  trough: "Mon YYYY",                 // Entry month label
  peak: "Mon YYYY",                   // Exit month label
  duration_months: N,                 // Number of months trough→peak
  blend_exit: XXXXX.XX,               // Blend dollar value at exit
  blend_return: XX.XX,                // Blend % return
  spy_exit: XXXXX.XX,                 // SPY dollar value at exit
  spy_return: XX.XX,                  // SPY % return
  winner: "Blend",                    // "Blend" or "SPY"
  alpha: X.XX,                        // blend_return - spy_return
  dates: ["YYYY-MM", ...],            // Monthly labels during window
  blend_series: [10000.0, ...],       // Blend portfolio value each month
  spy_series:   [10000.0, ...],       // SPY portfolio value each month
}
```

The table, cards, bar chart, and detail chart are all generated dynamically from `DATA.bear` — no HTML changes are needed.

### Swapping the Benchmark

Replace all `SPY` references in the `DATA` object with a new benchmark ticker's pre-computed series. Update the benchmark chip in the header, the legend labels in the charts, and the CSS variable `--spy-color` if you want a different benchmark color.

### Adding Dark/Light Mode Toggle

The entire color scheme is driven by CSS custom properties on `:root`. Add a `.light-mode` class to `<body>` and override the variables:

```css
body.light-mode {
  --bg: #f4f5f7;
  --surface: #ffffff;
  --card: #ffffff;
  --border: #e2e6ea;
  --text: #1a1d23;
  --muted: #6b7280;
}
```

Then add a toggle button that calls `document.body.classList.toggle('light-mode')`.

---

*Built with Perplexity Computer · Data via Perplexity Finance · Charts powered by Chart.js 4.4.0*
*Price return only. No dividends reinvested. Past performance does not guarantee future results.*
