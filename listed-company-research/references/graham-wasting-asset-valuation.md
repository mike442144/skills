# Graham Wasting Asset Valuation Method

## When to Apply

Use this method for companies where the core productive asset is **physically consumed** over time and cannot be replaced without finding new deposits:
- Mining companies (limestone, coal, metals, minerals)
- Oil & gas producers
- Quarry and aggregate producers
- Cement companies with captive limestone mines

**Core insight from Security Analysis (Graham & Dodd, 1934):**
When a factory/processing plant is built adjacent to a mine, the two form a single economic unit. Once the ore is exhausted, the specialized plant, rail spurs, and infrastructure lose most of their value (salvage minus relocation cost ≈ zero). The investor's capital is being physically depleted along with the resource.

## The Problem with Standard Metrics

For wasting asset companies:
- **EBIT / Market Cap** overstates true yield because it treats depreciation as a non-cash accounting entry, ignoring that the asset base is literally disappearing
- **Book value** is misleading because historical cost of the mine may be near zero (fully depleted) or may not reflect replacement cost of finding equivalent reserves
- **P/E ratio** ignores that earnings are partly a return OF capital, not just a return ON capital

---

## Method 1: Buyer's Depreciation (TABLE 36.4 — Homestake Mining)

**This is Graham's primary method.** It calculates the true investor yield by:
1. Separating the purchase price into "mine property" and "available assets"
2. Deducting a required return on available assets from EBITDA
3. Charging the investor's OWN depreciation (not the company's) against the mine purchase price
4. Computing the residual yield on the mine investment

### Step-by-step:

**Step 1 — Net Available Assets:**
```
Net Available Assets = Current Assets - Current Liabilities
```
(Conservative. Generous version adds Long-term Investments.)

**Step 2 — Paid for Mine Property:**
```
Paid for Mine = Market Cap - Net Available Assets
```
This is the price the investor effectively paid for the mine + plant package.

**Step 3 — Earnings Required on Available Assets:**
```
Required Return = Net Available Assets × 5%
```
(5% is the standard rate Graham used for safe, liquid assets.)

**Step 4 — Earnings on Mine Investment:**
```
Earnings on Mine = EBITDA - Required Return on Available Assets
```

**Step 5 — % Earned Before Depreciation:**
```
% Before Dep = Earnings on Mine / Paid for Mine
```

**Step 6 — Investor's Depreciation:**
The investor charges depreciation on the **mine purchase price** (not book value) at a rate determined by mine life:
```
Max depreciation rate = 1 / mine_life_years  (e.g., 1/27 = 3.65%)
Min depreciation rate = max_rate / 2          (conservative lower bound)
```

**Pitfall — do NOT use 5%-9% by default.** Graham used 5%-9% for Homestake because its mine life was ≥11 years (1/11 ≈ 9%). Always calculate the rate from actual mine reserve life.

**Step 7 — Earnings After Investor's Depreciation:**
```
Max earnings = Earnings on Mine - Min depreciation
Min earnings = Earnings on Mine - Max depreciation
```

**Step 8 — True Investor Yield:**
```
Max yield = Max earnings / Paid for Mine
Min yield = Min earnings / Paid for Mine
```

**Key insight:** The company's own depreciation charge is IGNORED. The investor recalculates depreciation based on their own purchase price and the mine's actual remaining life.

### Worked Example: Conch Cement (海螺水泥, 600585.SH) — 2025

**Data (百万元 CNY):**
- Market Cap: 106,800
- EBITDA: 17,246
- Current Assets: 88,805
- Current Liabilities: 27,819
- LT Investments: 25,389
- Company depreciation: 8,464
- Limestone reserves: 125.5 亿吨
- Annual extraction: 4.58 亿吨/年
- Mine life: 125.5 / 4.58 = 27.4 years
- Investor depreciation rate: 1/27.4 = 3.65% (max), 1.82% (min)

**Conservative scenario (Net Available = Current Assets - Current Liab = 60,986):**
```
Paid for Mine = 106,800 - 60,986 = 45,814
Required Return = 60,986 × 5% = 3,049
Earnings on Mine = 17,246 - 3,049 = 14,197
% Before Dep = 14,197 / 45,814 = 31.0%

Investor dep max (3.65%): 45,814 × 3.65% = 1,672
Investor dep min (1.82%): 45,814 × 1.82% = 836

Max earnings: 14,197 - 836 = 13,361 → yield 29.2%
Min earnings: 14,197 - 1,672 = 12,525 → yield 27.3%
```

**Generous scenario (Net Available = Current Assets + LT Investments - Current Liab = 86,375):**
```
Paid for Mine = 106,800 - 86,375 = 20,425
Required Return = 86,375 × 5% = 4,319
Earnings on Mine = 17,246 - 4,319 = 12,927
% Before Dep = 12,927 / 20,425 = 63.3%

Investor dep max (3.65%): 20,425 × 3.65% = 745
Investor dep min (1.82%): 20,425 × 1.82% = 373

Max earnings: 12,927 - 373 = 12,555 → yield 61.5%
Min earnings: 12,927 - 745 = 12,182 → yield 59.6%
```

**Note:** Company's own depreciation (8,464) is 5-23× higher than investor's depreciation (373-1,672). This is why book EBIT yield (8.3%) vastly understates true yield (27-62%).

---

## Method 2: Sinking Fund (Hoskold Formula)

Alternative approach. The investor receives annual cash flow (EBITDA), but must set aside part into a sinking fund at rate `i` that accumulates to the initial investment `P` over `n` years.

**Annual sinking fund contribution:**
```
S = P × [i / ((1+i)^n - 1)]
```

**True investor yield:**
```
Yield = (EBITDA - S) / P
```

Where: `P` = market cap, `n` = mine life years, `i` = sinking fund rate (typically 4-6%).

**Use Method 1 (Buyer's Depreciation) as the primary method.** The sinking fund is a simplified alternative when the mine purchase price and available assets are harder to separate.

---

## Critical Pitfall: Mine Life vs Equipment Life Mismatch

**The trap:** Using equipment depreciation years (15-25 years) as the asset life `n`, when the actual mine reserves last 30-50+ years.

**How to find mine life:**
1. Annual report: search for "矿山储量" (mine reserves), "石灰石资源" (limestone resources), "可采年限" (recoverable years)
2. Mining rights disclosure: "采矿权" sections list proven reserves and designed capacity
3. Credit rating reports often contain reserve data (search via ifind-repilot-announcement-search)
4. Baidu search for industry news articles citing reserve figures
5. Calculate: Reserve tons / Annual extraction tons = Mine life years

**If mine life >> equipment life:**
- The company can replace equipment and continue operating
- Use mine life as `n` in both methods
- True yield will be HIGHER than equipment-life calculation suggests

**If mine life ≈ equipment life:**
- Both deplete together; standard wasting asset method applies

## Data Requirements

From financial statements (Google Sheets or mx-finance-data):
- EBITDA
- Depreciation & Amortization (from Cash Flow statement, NOT income statement D&A line which is often blank)
- Total Current Assets, Total Current Liabilities
- Long-term Investments
- Net PP&E (for equipment life estimate as sanity check)
- Market Cap (current)

From annual report / mining disclosures:
- Proven & probable reserves (tons)
- Annual production/extraction rate (tons/year)

---

## A/H Dual-Listed Comparison

When the same wasting asset company trades on both A-share and HKEX at a discount:

**Higher intrinsic yield on H-share:** Same EBITDA, lower market cap → lower mine purchase price → higher true yield.

**But dividend yield may be lower on H-share after tax:** H-share dividends from mainland Chinese companies incur 20% withholding tax for individual investors via Stock Connect, while A-share dividends are tax-free after 1 year of holding.

**Permanent discount analysis — key question:** If the A/H discount NEVER closes, does the higher intrinsic yield matter?

- **Graham's framework assumes full acquisition** (buying the whole company). In that case, there is only one price, no discount, and the true yield is real.
- **For minority shareholders**, the higher H-share intrinsic yield only materializes if:
  1. The discount narrows (market correction)
  2. Management repurchases H-shares or privatizes
  3. Dividend payout increases enough that H-share pre-tax yield advantage overwhelms the 20% tax drag
  4. Dividend reinvestment at lower H-share prices compounds the advantage over decades

**If none of these happen**, the H-share investor has higher paper yield but lower actual cash flow (after-tax dividend), making A-share the better choice for income-focused investors.

**The practical conclusion:** H-share is only superior if you believe the discount will eventually converge, or if you plan to reinvest dividends for 20+ years. Otherwise A-share wins on cash-in-hand.

---

## Pitfall: Companies with Significant Investment Income

Some wasting asset companies also hold large investment portfolios (financial assets, trading securities, equity investments). When investment income is a material share of profits, the Graham method needs adjustment:

**Example: Tapai Group (塔牌集团, 002233.SZ) — 2025**
- Market Cap: 9,816 百万元
- EBITDA: 876 百万元
- Cash + ST Investments: 5,659 百万元 (58% of market cap)
- LT Investments: 2,086 百万元
- Net PP&E: 3,303 百万元 (small)
- Net Debt: -5,629 百万元 (cash far exceeds debt)
- Investment income: ~236 百万元 (37% of profit)
- Mine reserves: ~2.5 亿吨, mine life 10-15 years

This is a **"理财公司附带一个小水泥厂"** (investment company with a small cement plant). The EBITDA includes the cement operations but also captures investment returns. Graham's original framework assumed a pure wasting asset — when investment income is material, the "earnings on mine" figure is inflated.

**Adjustment approach:**
1. Separate operating EBITDA from investment income (look at "投资收益" in the income statement)
2. Apply the Graham method to operating EBITDA only
3. Value the investment portfolio separately (at book value or market value)
4. The "true yield" on the cement operation will be lower than the blended figure suggests

**In Tapai's case:** After separating investment income, the cement operation's true Graham yield is closer to 4-8% (conservative) vs 13-17% (generous), not dramatically higher than the 5.7% dividend yield.

---

## Pitfall: Limestone Self-Sufficiency Rate

For cement companies, the mine life calculation must account for **石灰石自给率** (limestone self-sufficiency rate). If the company purchases a significant portion of its limestone externally, the mine reserves only cover a fraction of total consumption.

**Formula:**
```
Actual extraction rate = Total limestone consumption × Self-sufficiency rate
Mine life = Reserves / Actual extraction rate
```

**Example: Conch Cement** has 98% self-sufficiency, so extraction ≈ consumption. **Smaller cement companies** may have 50-80% self-sufficiency, meaning their captive mines last longer than total consumption suggests, but they are also exposed to external limestone price volatility.

If self-sufficiency rate is unknown, check:
- Annual report "原材料" section for limestone procurement breakdown
- Credit rating reports (they often mention self-sufficiency)
- Industry news articles about mine acquisitions

---

## Pitfall: Small Cement Companies vs Industry Giants

Industry leaders (Conch, CNBM) typically have:
- Larger mine reserves (100+ 亿吨)
- Longer mine life (25-50 years)
- Higher self-sufficiency (90-98%)
- Lower Graham depreciation rates (2-4%)

Smaller regional players (Tapai, etc.) typically have:
- Smaller mine reserves (1-10 亿吨)
- Shorter mine life (10-20 years)
- Lower self-sufficiency (50-80%)
- Higher Graham depreciation rates (5-10%)

This means the same Graham method can produce **very different true yield ranges** for companies in the same industry, driven by reserve scale and mine life differences. Always verify reserve data — do not assume industry average.

---

## Source

Graham, Benjamin & Dodd, David. *Security Analysis* (1934, multiple editions).
- "Depreciation and Amortization Charges from the Investor's Standpoint" chapter
- TABLE 36.3: Homestake Mining Company earnings analysis (1925 vs 1938)
- TABLE 36.4: "Depreciation Calculation for the Buyer of Homestake Mining Company"
