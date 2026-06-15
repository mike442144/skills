# Google Sheet Financial Data Structure

User maintains **multiple industry-specific Google Sheets** containing CIQ-sourced financial data for tracked companies. These are the primary source for long-term (10+ year) financial trend analysis in appendix sections.

## Local CIQ Excel Files

The user maintains **two distinct types** of local CIQ-sourced Excel files. Check BOTH types for the target company.

### Type A: Google Sheet Exports (`.xlsx`, openpyxl)

These mirror the Google Sheet structure (one file with multiple tabs per company).

| File Path | Industry Focus | Known Tabs |
|-----------|----------------|------------|
| `/home/mike/hermes-workspace/装修建材.xlsx` | 建材/家居/水泥 | Summary、三一重工财务、鲁阳节能财务、伟星新材财务、中材国际财务、中材国际经营数据、慕思财务、金螳螂财务、金螳螂运营、塔牌水泥财务、海螺水泥财务、北新建材财务、顾家家居财务、欧派家居财务、东方雨虹财务、资本结构 |

**Data extraction pattern (local Excel):**
```python
import openpyxl
wb = openpyxl.load_workbook('/home/mike/hermes-workspace/装修建材.xlsx', data_only=True)
ws = wb['海螺水泥财务']  # tab naming: 公司名财务
# Header row (row 1): Col E=2007, F=2008, ..., W=2025
# Key Stats rows: label in col B (index 1), data starts at col E (index 4)
# Balance Sheet: starts at row ~131, label in col B
# Income Statement: starts at row ~39, label in col B
```

**Search for Type A files:** `find /home/mike -name "*.xlsx" -type f | grep -v node_modules`

### Type B: CIQ Financials Directory (`.xls`, xlrd)

A dedicated directory of **individually exported CIQ Financials files**, one per company:

**Path:** `/home/mike/hermes-workspace/CIQ_Financials/`

**File naming:** `<Company Full Name> <Exchange>:<Code> Financials.xls`
(e.g., `Huaxin Building Materials Group Co Ltd SHSE 600801 Financials.xls`)

**Format:** Old `.xls` format — use `xlrd`, NOT `openpyxl` (openpyxl does not support `.xls`).

**Sheet structure (13 sheets):**
| Sheet | Content |
|-------|---------|
| Key Stats | Summary metrics: Revenue, EBITDA, Net Income, DPS, EPS, Growth rates |
| Income Statement | Full IS: Revenue → COGS → Gross Profit → SG&A → Op Income → Net Income |
| Balance Sheet | Full BS: Current Assets → Total Assets → Current Liab → Total Liab → Equity |
| Cash Flow | Full CF: Cash from Ops, CAPEX, Cash from Investing, Cash from Financing, Dividends, FCF |
| Multiples | Valuation multiples by year |
| Historical Capitalization | Market cap history |
| Capital Structure Summary | Debt/equity breakdown |
| Capital Structure Details | Detailed debt instruments |
| Ratios | ROA/ROE, margins, turnover, liquidity, solvency ratios (20+ years) |
| Supplemental | Supplementary line items |
| Industry Specific | Industry-specific metrics |
| Pension OPEB | Pension and OPEB data |
| Segments | Revenue/D&A by business segment (Cement Sales, Concrete, Aggregate, etc.) |

**Data extraction pattern (Type B):**
```python
import xlrd
wb = xlrd.open_workbook('/home/mike/hermes-workspace/CIQ_Financials/Huaxin Building Materials Group Co Ltd SHSE 600801 Financials.xls')
ws = wb.sheet_by_name('Income Statement')
# Row 14: year headers (e.g., "12 months\nDec-31-2025")
# Row 15: currency (all CNY)
# Row 17+: line items with values in columns 1..N
# Units: S&P Capital IQ default = CNY Millions
# Years: typically 19+ years (2007-2025+)

# Key rows in Income Statement (row index):
# 17: Revenue, 19: Total Revenue, 20: COGS, 21: Gross Profit
# 22: SG&A, 23: D&A, 24: Operating Income
# 25: Interest Expense, 34: Income Tax, 35: Net Income to Company
# 37: Net Income, 40: Basic EPS, 42: Diluted EPS
# 44: Weighted Avg Basic Shares, 46: Diluted Shares
# 48: DPS, 49: Payout Ratio %

# Key rows in Cash Flow (row index):
# 17: Cash from Ops, 18: CAPEX, 19: Free Cash Flow
# 20: Cash from Investing, 21: Cash from Financing
# 24: Dividends Paid, 25: Net Change in Cash
```

**Value of Type B files:** Contains **19+ years** of fully standardized CIQ data with 13 distinct analytical sheets. Far richer than Type A for balance sheet detail, cash flow breakdown, ratios, and segment analysis. Always check this directory FIRST for long-cycle financial history.

**Known units:** All financial figures in **CNY Millions** (S&P CIQ default). Ratios as decimals (0.296 = 29.6%). EPS in CNY.

## Known Sheets

| Sheet Name | Sheet ID | Industry Focus | Known Tabs |
|------------|----------|----------------|------------|
| (default/industrial) | `1GsHpzk06-vPt9kDh6dD1tfAqf84BobWAnbkT6S29VV4` | 建材/家居/消费 | 三一重工、鲁阳节能、伟星新材、中材国际、慕思、金螳螂、塔牌集团、海螺水泥、北新建材、顾家家居、欧派家居、东方雨虹 |
| 汽车 | `100LHzykrulKYOSwZhjOwAIir5FjntN45O_thcXos3O8` | 汽车/零部件/新能源 | 理想汽车、福耀玻璃、郑煤机、宁波华翔、三角轮胎、赛轮轮胎、华域汽车、中鼎股份、宁德时代 |

**IMPORTANT — Discovery workflow:** Do NOT assume the target company is in the default sheet. If the company's tab is not found in the first sheet checked, use Google Drive API to search for other spreadsheets:
```python
drive_service.files().list(q="mimeType='application/vnd.google-apps.spreadsheet'", fields='files(id,name)').execute()
```
Then check each spreadsheet's tabs for the target company. The user may have additional sheets not listed above.

## Sheet Tab Naming Convention

Each company has its own tab named `公司名财务` (e.g., `顾家家居财务`, `欧派家居财务`). There is also a `Summary` tab.

To list all tabs:
```python
import sys; sys.path.insert(0, '/home/mike/.hermes/hermes-agent')
from google.oauth2.credentials import Credentials
from googleapiclient.discovery import build

creds = Credentials.from_authorized_user_file('/home/mike/.hermes/google_token.json')
service = build('sheets', 'v4', credentials=creds)
spreadsheet = service.spreadsheets().get(
    spreadsheetId="1GsHpzk06-vPt9kDh6dD1tfAqf84BobWAnbkT6S29VV4",
    fields='sheets(properties(title,sheetId,gridProperties))'
).execute()
for s in spreadsheet['sheets']:
    print(s['properties']['title'])
```

## Column Structure

**Header row (row 1):**
- Cols A-C: labels (A=section marker, B=label, C=sub-label)
- Col D: Unit
- Cols E-W: Annual data, where E=2007, F=2008, ..., W=2025 (year = 2007 + col_index - 4)
- Col X: LTM (latest 12 months, e.g., "LTM 12 months Mar-31-2026")
- Cols AA-AU: Quarterly data (Q1 2021 through Q1 2026)

**Year mapping for data columns (0-indexed from row array):**
- index 4 = col E = 2007
- index 5 = col F = 2008
- ...
- index 22 = col W = 2025
- index 23 = col X = LTM

Formula: `year = 2007 + (array_index - 4)` for indices 4-22; index 23 = LTM.

## Row Structure

### Key Stats Section (rows 3-27)
- Row 3: section header ("盈利指标")
- Label in column C (index 2)
- Rows include: Net Working Capital, Net Income, Gross Margin, Op. Margin, Net Margin, ROE, Interest Coverage Ratio, D&A, Common Equity, Tangible Book Value, DPS, Total Liabilities And Equity, Cash from Ops., FCFF, Basic EPS, Net Debt, EBITDA, 扣非净利润, SBC, Total Revenue, ROIC

### Income Statement Section (rows 37+)
- Label in column B (index 1)
- Key rows: 39=Total Revenue, 41=COGS, 42=Gross Profit, 52=Operating Income, 77=Net Income, 85=Basic EPS, 96=Dividends per Share, 97=Payout Ratio %

### Balance Sheet & Cash Flow
- Located after IS section (typically rows 120+)
- Read row labels to find specific items

## Data Extraction Pattern

```python
# Read key metrics for a company
sheet_id = "1GsHpzk06-vPt9kDh6dD1tfAqf84BobWAnbkT6S29VV4"
tab_name = "顾家家居财务"

key_rows = {
    5: "Net Income",
    6: "Gross Margin",
    7: "Op. Margin",
    8: "Net Margin",
    9: "ROE",
    16: "DPS",
    21: "Basic EPS",
    23: "EBITDA",
    24: "扣非净利润",
    26: "Total Revenue",
    27: "ROIC",
    39: "Total Revenue (IS)",
    42: "Gross Profit",
    52: "Operating Income",
    77: "Net Income (IS)",
    85: "Basic EPS (IS)",
    96: "DPS (IS)",
    97: "Payout Ratio",
}

for row_num, label in key_rows.items():
    r = service.spreadsheets().values().get(
        spreadsheetId=sheet_id,
        range=f"'{tab_name}'!A{row_num}:X{row_num}"
    ).execute()
    vals = r.get('values', [[]])[0]
    # Data starts at index 4 (col E = 2007)
    for i in range(4, min(len(vals), 24)):
        year = 2007 + (i - 4) if i < 23 else "LTM"
        v = vals[i] if i < len(vals) else ""
        if v and v != "NA":
            print(f"{year}: {v}")
```

## Pitfalls

- **Unit**: Financial figures are in millions of RMB (百万元) unless noted otherwise
- **NA values**: Early years may have "NA" for metrics not yet tracked
- **Sparse rows**: Some rows return fewer than 24 elements — always check `len(vals)` before indexing
- **Label position varies**: Key Stats rows have label at index 2 (col C); IS rows have label at index 1 (col B). Check both.
- **DPS adjustment**: The sheet may store raw (unadjusted) DPS. Apply stock split/bonus adjustment (10转X) retroactively when presenting per-share data alongside EPS.
- **Companies not in sheet**: If a company tab doesn't exist, note it and fall back to mx-finance-data (3-year limit) with explicit limitation callout.
