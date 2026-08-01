# Google Sheet Financial Data Structure

User maintains **multiple industry-specific Google Sheets** containing CIQ-sourced financial data for tracked companies. These are the primary source for long-term (10+ year) financial trend analysis.

## Coverage Limitations

The Google Sheets contain CIQ's standard financial sheets: **Income Statement, Balance Sheet, Cash Flow** (with 19+ years of data), and user maintained evaluations(Summary Sheet) and indicators(Company Sheet). For data like NPL ratios, loan composition, segment revenue breakdowns, etc., use annual report appendices or credit rating reports instead.

**Known units:** All financial figures in **CNY Millions** (S&P CIQ default). Ratios as decimals (0.296 = 29.6%). EPS in CNY.

## Sheet Discovery (Drive API only — no static list)

Discovery workflow (two steps):

**Step 1 — List all spreadsheets:**

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

**Step 2 — Check tabs in each spreadsheet to locate the target company:**

```python
sheets_svc = build('sheets', 'v4', credentials=creds)
for sid, name in [(f['id'], f['name']) for f in res.get('files', [])]:
    sp = sheets_svc.spreadsheets().get(spreadsheetId=sid, fields='sheets(properties(title))').execute()
    tabs = [s['properties']['title'] for s in sp['sheets']]
    hit = [t for t in tabs if '<公司名>' in t or '<代码>' in t]
    if hit:
        print(f'{name}: {hit}')
```

Once a tab is found, extract data using the Column/Row Structure below. If no tab matches after checking all spreadsheets, record "Google Sheets: no data for this company" in report §6.3 and fall back to mx-finance-data (3-year limit; explicitly note the limitation).

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
    spreadsheetId="<spreadsheet id from discovery step>",
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

## Priority Metrics for Cross-Validation

When using Google Sheets for same-CIQ peer cross-validation, prioritize metrics that Wind batch queries often miss or return inconsistently:

- ROIC
- Net Debt
- DPS (Dividends per Share)
- Payout Ratio
- Interest Coverage Ratio

These are all present in the Key Stats section (rows 3-27) and provide the longest continuous history (2007+).

## Data Extraction Pattern

```python
# Read key metrics for a company (spreadsheet id from discovery step above)
sheet_id = "<spreadsheet id from discovery step>"
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

- **Unit**: Financial figures are in millions of RMB unless noted otherwise
- **NA values**: Early years may have "NA" for metrics not yet tracked
- **Sparse rows**: Some rows return fewer than 24 elements — always check `len(vals)` before indexing
- **Label position varies**: Key Stats rows have label at index 2 (col C); IS rows have label at index 1 (col B). Check both.
- **DPS adjustment**: The sheet may store raw (unadjusted) DPS. Apply stock split/bonus adjustment (bonus issue / stock dividend) retroactively when presenting per-share data alongside EPS.
- **Companies not in sheet**: If a company tab doesn't exist, note it and fall back to mx-finance-data (3-year limit) with explicit limitation callout.
