# Google Sheet Financial Data Structure

User maintains **multiple industry-specific Google Sheets** containing CIQ-sourced financial data for tracked companies. These are the primary source for long-term (10+ year) financial trend analysis in appendix sections.

## Coverage Limitations

The Google Sheets contain a subset of CIQ's standard sheets: **Key Stats, Income Statement, Balance Sheet, Cash Flow, Ratios** (with 19+ years of data). They do **NOT** contain CIQ's `Industry Specific`, `Segments`, `Pension OPEB`, or `Supplemental` sheets. For data typically found in those sheets (NPL ratios, loan composition, segment revenue breakdowns, etc.), use annual report appendices or credit rating reports instead.

**Known units:** All financial figures in **CNY Millions** (S&P CIQ default). Ratios as decimals (0.296 = 29.6%). EPS in CNY.

## Sheet Discovery (Drive API only — no static list)

**IMPORTANT — 本 skill 不维护静态 sheet id / 公司清单**（skills 仓库为公开 GitHub 仓库，不可存放敏感标识）。每次研究必须通过 Google Drive API 实时发现目标公司所在的 spreadsheet 与 tab。**禁止假设"某行业无表"或"目标公司只在某张表里"**——用户实际维护 30+ 张行业表（饮料/食品/餐饮/医药/汽车/化工/建材/家电/传媒/地产/金融/纺织服装等），tab 命名统一为 `<公司名>财务`（部分公司另有配套 `<公司名>运营数据` tab，由用户手工维护）。

Discovery workflow（两步）：

**Step 1 — 列出所有 spreadsheet：**

```python
import sys; sys.path.insert(0, '/home/mike/.hermes/hermes-agent')
from google.oauth2.credentials import Credentials
from googleapiclient.discovery import build

creds = Credentials.from_authorized_user_file('/home/mike/.hermes/google_token.json')
drive = build('drive', 'v3', credentials=creds)
res = drive.files().list(q="mimeType='application/vnd.google-apps.spreadsheet'", fields='files(id,name)').execute()
for f in res.get('files', []):
    print(f['id'], '|', f['name'])
```

**Step 2 — 逐个检查 spreadsheet 的 tabs，定位目标公司：**

```python
sheets_svc = build('sheets', 'v4', credentials=creds)
for sid, name in [(f['id'], f['name']) for f in res.get('files', [])]:
    sp = sheets_svc.spreadsheets().get(spreadsheetId=sid, fields='sheets(properties(title))').execute()
    tabs = [s['properties']['title'] for s in sp['sheets']]
    hit = [t for t in tabs if '<公司名>' in t or '<代码>' in t]
    if hit:
        print(f'{name}: {hit}')
```

命中后按下方 Column/Row Structure 提取数据。若全部检查后无命中，在报告 §6.3 记录"Google Sheets 无该公司数据"，回退到 mx-finance-data（3 年限制，需显式标注局限性）。

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
    spreadsheetId="<发现流程中命中的 spreadsheet id>",
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
# Read key metrics for a company (spreadsheet id 来自上方发现流程)
sheet_id = "<发现流程中命中的 spreadsheet id>"
tab_name = "<公司名>财务"

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
