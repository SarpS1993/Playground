# ING Investments — European Investment Landscape: Research Notes
**Compiled:** 2026-04-13  
**Target Period:** FY2025 (most recent available: FY2024 / Q4 2024)  
**Researcher:** Claude Code (claude-sonnet-4-6)

---

## Methodology

- Primary data source: EFAMA Quarterly Statistical Release Q4 2024 (Release N°100, March 2025)
- Market AUM = EFAMA total net assets of UCITS + AIFs **domiciled** in each country (not managed-by-residents)
- AUC (self-directed brokerage) = no single official European aggregate; competitor-level AUC sourced from individual annual reports
- Population data: Eurostat / Worldometer 2024 estimates
- Population 18+: estimated from Eurostat age structure (~83–85% of total for these countries)
- All monetary values normalized to €bn
- PLN→EUR conversion: 4.27 PLN/EUR (2024 average rate)

---

## BATCH 1 — PAN-EUROPEAN AGGREGATORS

### EFAMA Quarterly Statistical Release Q4 2024
**URL:** https://www.efama.org/sites/default/files/files/quarterly-statistical-release-q4-2024.pdf  
**Status:** FOUND (PDF extracted via pdfplumber)  
**Period:** End Q4 2024 (December 31, 2024)  
**Release:** N°100, March 2025

Total net assets (UCITS + AIFs domiciled), Table 10:
- Germany: **€2,808,290.1 million = €2,808.3 bn**
  - UCITS: €549,130.6m | AIFs: €2,259,159.5m
- Belgium: **€230,506.9 million = €230.5 bn**
  - UCITS: €229,111.8m | AIFs: €1,395.1m
- Spain: **€417,542.0 million = €417.5 bn**
  - UCITS: €381,225.0m | AIFs: €36,317.0m
- Italy: **€443,404.5 million = €443.4 bn**
  - UCITS: €309,701.5m | AIFs: €133,703.0m
- Netherlands: **€902,121.8 million = €902.1 bn**
  - UCITS: €98,334.7m | AIFs: €803,787.1m
- Poland: **€88,721.7 million = €88.7 bn**
  - UCITS: €36,781.5m | AIFs: €51,940.2m

Net inflows 2024 YTD (Table 7):
- Germany: €27,734.1m | Belgium: €7,998.2m | Spain: €26,480.0m
- Italy: €16,114.1m | Netherlands: -€15,799.7m | Poland: €9,878.4m

**Note:** Netherlands has large institutional AIF sector (~€804 bn) — pension-like structures. UCITS retail funds are much smaller at €98 bn.

### Population Data
**URL:** https://www.worldometers.info/population/countries-in-europe-by-population/  
**Status:** FOUND  
**Period:** 2024 estimates

Total population:
- Germany: 84.4 million | Belgium: 11.4 million | Spain: 48.1 million
- Italy: 59.0 million | Netherlands: 17.2 million | Poland: 36.8 million

Population 18+ (estimated, ~83–85% of total based on Eurostat age structure):
- Germany: ~70.8 million | Belgium: ~9.7 million | Spain: ~39.5 million
- Italy: ~49.2 million | Netherlands: ~14.6 million | Poland: ~30.5 million

---

## BATCH 2 — NATIONAL FUND ASSOCIATIONS

### BVI (Germany)
**URL:** https://funds-europe.com/german-fund-industry-grew-by-9-year-on-year-shows-bvi-data/  
**Status:** FOUND (partial)  
- AUM as of Q1 2024: €4,289 bn (total managed by BVI members including foreign-domiciled funds)
- BVI members total: >€4,800 bn
- **Note:** BVI figures are manager-based (domicile-agnostic), EFAMA figures are domicile-based. Germany's €2,808 bn is domicile-only; BVI ~€4,800 bn includes German managers' Luxembourg/Ireland domiciled funds.

### Inverco (Spain)
**URL:** https://www.inverco.es  
**Status:** PARTIAL
- July 2024: €376.5 bn in domestic investment funds
- March 2025: €408.7 bn (for context)
- Consistent with EFAMA Q4 2024 figure of €381 bn (UCITS)

### Assogestioni (Italy)
**URL:** https://www.assogestioni.it  
**Status:** UNAVAILABLE (Italian language, no parseable English stats page found)
- Fallback: EFAMA domicile data used (€443.4 bn)
- Note: Assogestioni tracks Italian-domiciled AND Italian-managed assets separately

### DUFAS (Netherlands)
**URL:** https://www.dufas.nl  
**Status:** NOT FETCHED (dedicated website not searched)
- Fallback: EFAMA domicile data used (€902.1 bn)

### BEAMA (Belgium)
**URL:** https://www.beama.be  
**Status:** PARTIAL (EFAMA fact sheet hosted on BEAMA website found)
- Fallback: EFAMA domicile data used (€230.5 bn)

### IZFiA (Poland)
**URL:** https://www.izfia.pl  
**Status:** NOT FETCHED
- Fallback: EFAMA domicile data used (€88.7 bn)
- Additional source: Poland investment funds market ~€95 bn per research market reports (includes cross-border managed)

---

## BATCH 3 — COMPETITORS

### GERMANY

#### DWS Group (Deutsche Bank Asset Management)
**URL:** https://www.dws.com/our-profile/facts-and-figures/  
**Status:** FOUND  
- AUM: **€1,012 bn** (end Q4 2024)
- Mid-2024 AUM: €933 bn (Q2 2024)
- Revenue: Not segmented from Deutsche Bank Group (DWS profit before taxes ~€1.3 bn est.)
- Clients: Not publicly disclosed separately
- Period: Q4 2024

#### Union Investment (DZ Bank)
**URL:** https://www.dzbank.com (DZ Bank press release Jan 2025)  
**Status:** FOUND  
- AUM: **€504.7 bn** (end 2024, up from €455.2 bn in 2023)
- Net retail inflows: €12.6 bn; institutional: €4.7 bn
- Profit before taxes: €1.2 bn
- Period: FY2024

#### DekaBank (Sparkassen-Finanzgruppe)
**URL:** https://www.sparkassenstiftung.de (Financial Report 2024)  
**Status:** FOUND  
- AUM: **€416 bn** (end 2024)
- Investment fund AUM: €43.5 bn (retail funds specifically)
- Customer accounts: ~5.7 million
- Period: FY2024

#### Commerzbank
**URL:** https://www.commerzbank.de (Press release Feb 2025)  
**Status:** FOUND (partial)  
- AUM: **>€400 bn** (private & small-business customers)
- Securities volume: **€243 bn** (end Q4 2024, up from €215 bn in Q4 2023)
- Net commission income: €3.64 bn (+7% YoY)
- Period: FY2024
- Note: Comdirect merged into Commerzbank in 2020; data combined

### BELGIUM

#### KBC Group
**URL:** https://www.kbc.com (Q4 2024 Quarterly Report)  
**Status:** FOUND  
- AUM: **€276 bn** (end December 2024, group-wide: Belgium + Czech + International)
- Growth: +13% YoY
- Net inflows: +3 pp of YoY growth
- Period: Q4 2024
- Note: Belgium Business Unit is ~55% of group revenue; Belgian AUM estimate ~€150 bn

#### Belfius
**URL:** https://www.belfius.be (Press release 2024 results)  
**Status:** FOUND  
- Private & Wealth total savings and investments: **€61.4 bn** (end 2024)
- Discretionary asset management (AUM): **€54.6 bn** (updated figure)
- Customers using asset management: ~172,000 (+8%)
- Market share: 15.2%
- Net profit: €1.127 bn (record)
- Period: FY2024

#### BNP Paribas Fortis
**URL:** https://www.bnpparibasfortis.com  
**Status:** PARTIAL  
- Total bank assets: €266.89 bn (2024) — balance sheet, not AUM
- Specific investment AUM for Belgium operations: No official public disclosure found
- Note: Part of BNP Paribas Group which manages >€1.6 tn globally

#### Argenta
**Status:** NOT FOUND — No publicly disclosed AUM for 2024

#### Beobank
**Status:** NOT FOUND — Smaller player; no public AUM disclosure

### SPAIN

#### CaixaBank
**URL:** https://www.caixabank.com (news release)  
**Status:** FOUND  
- Wealth Management AUM: **€189.22 bn** (end 2024, private banking)
- CaixaBank AM market share: **23.7%** of Spain investment funds (Dec 2024)
- CaixaBank AM surpassed €100 bn in domestic fund AUM (historic record)
- Period: Q4 2024

#### Santander Spain
**URL:** https://www.santanderassetmanagement.com  
**Status:** FOUND  
- Santander AM Spain AUM: **€105 bn** (end 2024)
- Net inflows: €5.4 bn in 2024
- Santander Group AM total: €256 bn (10 countries)
- Period: FY2024

#### Bankinter
**URL:** https://www.bankinter.com (press release 2024 results)  
**Status:** FOUND  
- Off-balance sheet managed funds (own + 3rd party + pensions + managed portfolios): **€57.7 bn** (+22.2%)
- Own investment fund AUM: **€16 bn** (+24.1%)
- Period: FY2024

#### BBVA Spain
**URL:** https://shareholdersandinvestors.bbva.com  
**Status:** PARTIAL  
- Customer funds: Grew by 5% with positive performance of mutual funds, pension funds
- Specific Spain AUM: Not publicly disclosed separately
- Net profit Spain: €3,784 mn (+39%)

#### Banco Sabadell
**URL:** https://comunicacion.grupbancsabadell.com  
**Status:** FOUND  
- Amundi partnership AUM: **€21.97 bn** (53.5% growth vs prior period)
- Discretionary portfolios (Sabadell Portfolios): **€1.84 bn** (+59%)
- Total investment AUM estimate: **~€24 bn**
- Period: 2024

### ITALY

#### Intesa Sanpaolo
**URL:** https://group.intesasanpaolo.com (FY2024 press release)  
**Status:** FOUND (partial)  
- Customer financial assets: **~€1.4 trillion** (total: deposits + AUM + administered)
- Insurance AUM: €177.3 bn (vs €171.6 bn Dec 2023)
- Net inflows into AuM: €5.1 bn in Q4 2024
- Wealth management leadership with €900 bn in deposits+AUM
- Period: FY2024
- Note: Total financial assets includes deposits; pure AUM is a subset

#### UniCredit
**URL:** https://www.unicreditgroup.eu (FY2024 press release)  
**Status:** PARTIAL  
- Net revenue: €24.2 bn (+4%); fees: €8.1 bn (+8%)
- AUM via Amundi partnership: **~€100 bn**
- ESG AuM penetration: 53%
- Specific total AUM not publicly segmented
- Period: FY2024

#### Fineco Bank
**URL:** https://images.fineco.it (FY2024 press release)  
**Status:** FOUND  
- Total Financial Assets (TFA): **€140.8 bn**
- AUM (managed assets): **€66.4 bn**
- Fineco AM AUM: €36.8 bn (retail: €25.0 bn, institutional: €11.8 bn)
- Total customers: **1,655,649** (new customers: 152,357 in 2024)
- Investment revenue: €369.5 mn (net commissions, investing area)
- Period: FY2024

#### Banca Mediolanum
**URL:** https://www.bancamediolanum.it (FY2024 results)  
**Status:** FOUND  
- AUM/AUA (total assets under management and administration): **€150.4 bn** (end 2024)
- Net inflows: €11.64 bn (+11% YoY)
- Net commission income: €1.31 bn (+12%)
- CET1: 23.0%
- Period: FY2024

#### Azimut Holding
**URL:** https://www.azimutinvestments.com (FY2024 press release)  
**Status:** FOUND  
- AUM: **€70.3 bn** (end Dec 2024, up from €60.6 bn in 2023, +16%)
- Assets under Administration: **€37.2 bn**
- Total assets: **€107.5 bn**
- Net inflows: €18.3 bn (2024)
- Period: FY2024

### NETHERLANDS

#### ABN AMRO
**URL:** https://www.abnamro.com (Integrated Annual Report 2024)  
**Status:** FOUND  
- Total investments overseen (wealth management): **€239 bn**
- Private banking presence in NL, Germany, France, Belgium
- Net profit: €2.4 bn; ROE >10%
- Period: FY2024

#### Van Lanschot Kempen
**URL:** https://newsroom.vanlanschotkempen.com (2024 annual results press release)  
**Status:** FOUND  
- AUM: **€149.3 bn** (+17%; vs 10% target)
- Net inflows: €9.2 bn
- Net profit: €141.9 mn (+13%)
- Private Clients NL net inflows: €2.1 bn; Belgium: €2.2 bn
- Period: FY2024

#### flatexDEGIRO (DEGIRO)
**URL:** https://flatexdegiro.com (Preliminary Results 2024)  
**Status:** FOUND  
- AUC (customer assets under custody): **€71.5 bn** (up from €51.7 bn in 2023, +38%)
- Securities: €67.2 bn | Cash: €4.3 bn
- Total customers: **3.1 million** (+13.8% YoY)
- Revenue: €480 mn (+23%)
- Transactions: 63.0 million in 2024
- Period: FY2024

#### Rabobank
**URL:** https://www.rabobank.com (Annual Results 2024)  
**Status:** NOT FOUND for investment AUM  
- Net profit: €5.163 bn | ROE: 10.0%
- No publicly disclosed investment/wealth AUM breakdown
- Note: Rabobank serves primarily agricultural/food sector; retail investing smaller

### POLAND

#### PKO Bank Polski (PKO TFI)
**URL:** https://www.pkobp.pl (press release 2024)  
**Status:** FOUND (partial)  
- Market share: **21.4%** of Polish investment funds for individuals (largest)
- Polish market total: ~€86–95 bn
- PKO TFI AUM estimate: ~€19–20 bn (21.4% of ~€88.7 bn)
- Period: FY2024

#### Bank Pekao (Pekao TFI)
**URL:** https://pekaotfi.pl  
**Status:** FOUND  
- Pekao TFI AUM: **PLN 32.2 bn** = **~€7.5 bn** (end Dec 2024, +32.9% YoY)
- Clients: ~500,000
- Period: December 2024

#### mBank
**Status:** NOT FOUND — No publicly disclosed investment AUM

#### Santander Bank Polska
**Status:** PARTIAL — Part of Santander Group; Poland segment AUM not segmented

#### BNP Paribas Polska
**Status:** PARTIAL — Part of BNP Paribas Group; Poland segment not segmented

---

## DATA GAPS SUMMARY

| Country | Market AUC | Notes |
|---------|-----------|-------|
| DE | No official data | No single official AUC/brokerage aggregate |
| BE | No official data | Same |
| ES | No official data | Same |
| IT | No official data | Same |
| NL | No official data | Same |
| PL | No official data | Same |

AUC data gap applies universally — no EU country publishes a single official "total self-directed brokerage assets" figure comparable to EFAMA's AUM data.

---

## SOURCES INDEX

1. EFAMA Q4 2024: https://www.efama.org/sites/default/files/files/quarterly-statistical-release-q4-2024.pdf
2. DWS Facts & Figures: https://www.dws.com/our-profile/facts-and-figures/
3. DZ Bank 2024 results: https://www.dzbank.com/content/dzbank/en/home/we-are-dz-bank/press/news_archive/2025/preliminary-results-for-2024--dz-bank-group-reports-a-profit-bef.html
4. DSGV Financial Report 2024: https://www.sparkassenstiftung.de/fileadmin/160923_Sparkassenstiftungen_HTML/downloads/Infopaper_Imagebrosch%C3%BCre/Publikationen_Sparkassen-Finanzgruppe/Financial_Report_2024.pdf
5. Commerzbank 2024 results: https://www.commerzbank.de/group/newsroom/press-releases/2025/20250213-pr-financial-results-2024.pdf
6. KBC Q4 2024: https://wcmassets.kbc.be/content/dam/kbccom/doc/investor-relations/Results/4q2024/4q2024-quarterly-report--en.pdf.cdn.res/last-modified/1739443462911/4q2024-quarterly-report--en.pdf
7. Belfius 2024 results: https://www.belfius.be/about-us/dam/corporate/press-room/press-articles/downloads/en/2025/Press%20release%20results%20for%202024.pdf
8. CaixaBank news: https://www.caixabank.com/en/headlines/news/caixabank-am-surpasses-100-billion-in-assets-managed-in-domestic-investment-funds-setting-a-historic-record
9. Santander AM: https://www.santanderassetmanagement.com/about-us/key-figures
10. Bankinter 2024: https://www.bankinter.com/webcorporativa/en/communication-room/news/institutional/bankinter-closes-2024-with-record-profit-of-953-million-euros--up-13--on-2023--thanks-to-increased-commercial-activity-and-diversification-of-the-business
11. Sabadell-Amundi: https://comunicacion.grupbancsabadell.com/en/press-room/banco-sabadell-and-amundi-mark-five-years-of-their-strategic-partnership-with-a-53-increase-in-assets-under-management/
12. Intesa Sanpaolo FY2024: https://group.intesasanpaolo.com/en/investor-relations/press-releases/2025/02/20250204-ris-fy24-uk
13. Fineco FY2024: https://images.fineco.it/pub/pdf/chi-siamo/comunicati-ipo/2025-02-06-PR_FY24_Results.pdf
14. Banca Mediolanum FY2024: https://www.bancamediolanum.it/static-assets/documenti/file/it/2025/02/06/Presentazione_FY2024.pdf
15. Azimut FY2024: https://www.azimutinvestments.com/documents/45685/922174/Press_release_FY_2024_vF.pdf/474216c1-9b57-8d7e-5c9d-09f016e587bc
16. ABN AMRO 2024: https://www.abnamro.com/en/news/abn-amro-publishes-its-integrated-annual-report-2024
17. Van Lanschot Kempen 2024: https://newsroom.vanlanschotkempen.com/en/van-lanschot-kempen-publishes-2024-annual-results
18. flatexDEGIRO 2024: https://flatexdegiro.com/media/pages/investor-relations/reports-financial-calendar/1e7b76d00e-1740466733/250225-flatexdegiro_financial-release_preliminary-results-2024.pdf
19. PKO Bank Polski 2024: https://www.pkobp.pl/en/media/news/ahead-of-the-market-the-pko-bank-polski-group-presents-its-results-for-2024
20. Pekao TFI: https://pekaotfi.pl
