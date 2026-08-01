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

Once a tab is found, extract data using the structure guide below. If no tab matches after checking all spreadsheets, record "Google Sheets: no data for this company" in report §6.3 and fall back to other financial skills(may have n-year limit; explicitly note the limitation).

**Peer company tabs:** The same spreadsheet may contain tabs for industry peers (e.g., the 饮料 sheet has tabs for multiple beverage companies). During discovery, note any competitor tabs found — if present, extract and use in Chapter 3 (Competitive Landscape). If no peer tabs exist, ignore and use other sources.

## Sheet Tab Naming Convention

Each company has its own tab. Some use `公司名财务` (e.g., `青岛啤酒财务`), others use just the company name without the suffix. Always discover tabs dynamically (see code above) rather than assuming a fixed naming pattern. There is also a `Summary` tab in each spreadsheet.

## Column Structure (stable across all Sheets)

**Header row (row 1):**
- Cols A-C: labels (A=section marker, B=sub-section header, C=metric label in Key Stats)
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

## Row Structure (general pattern — do NOT hardcode row indices)

Each tab is organized into sections, delimited by **section markers in column A**:

```
Row 1:  (header)
Row 2:  A = "Key Stats"          ← section marker
        ...metrics (labels in col C)...
        B = "盈利指标"            ← sub-section
        B = "同比增速"            ← sub-section
Row N:  A = "Income Statement"   ← section marker
        ...line items (labels in col B)...
Row M:  A = "Balance Sheet"      ← section marker
        ...line items (labels in col B)...
Row K:  A = "Cash Flow"          ← section marker
        ...line items (labels in col B)...
```

**Key rules:**
- Key Stats metrics have labels in **col C** (index 2); the three financial statements have labels in **col B** (index 1).
- Row positions vary across Sheets (different companies have different line items). **Never hardcode row indices.** Always scan labels first, then extract by matched row.
- Section boundaries are identified by non-empty values in col A.

## Data Extraction Pattern

```python
import sys; sys.path.insert(0, '/home/mike/.hermes/hermes-agent')
from google.oauth2.credentials import Credentials
from googleapiclient.discovery import build

creds = Credentials.from_authorized_user_file('/home/mike/.hermes/google_token.json')
service = build('sheets', 'v4', credentials=creds)
sheet_id = "<spreadsheet id from discovery>"
tab_name = "<company tab name>"

# Step 1: Scan labels to build a row map (read enough rows to cover Key Stats)
r = service.spreadsheets().values().get(
    spreadsheetId=sheet_id,
    range=f"'{tab_name}'!A1:C40"
).execute()
rows = r.get('values', [])

row_map = {}  # label -> row_number
for i, row in enumerate(rows):
    label = row[2] if len(row) > 2 and row[2] else (row[1] if len(row) > 1 else "")
    if label:
        row_map[label] = i + 1  # 1-indexed

# Step 2: Extract data for desired metrics
targets = ["Net Income", "Gross Margin", "Op. Margin", "Net Margin", "ROE",
           "Dividends per Share", "Basic EPS", "EBITDA", "扣非净利润",
           "Total Revenue", "ROIC (资本来源法)"]

for label in targets:
    if label not in row_map:
        print(f"NOT FOUND: {label}")
        continue
    row_num = row_map[label]
    r = service.spreadsheets().values().get(
        spreadsheetId=sheet_id,
        range=f"'{tab_name}'!A{row_num}:X{row_num}"
    ).execute()
    vals = r.get('values', [[]])[0]
    print(f"\n{label} (row {row_num}):")
    for i in range(4, min(len(vals), 24)):
        year = 2007 + (i - 4) if i < 23 else "LTM"
        v = vals[i] if i < len(vals) else ""
        if v and v != "NA":
            print(f"  {year}: {v}")
```

## Pitfalls

- **Unit**: Financial figures are in millions of RMB unless noted otherwise.
- **NA values**: Early years may have "NA" for metrics not yet tracked.
- **Sparse rows**: Some rows return fewer than 24 elements — always check `len(vals)` before indexing.
- **DPS adjustment**: The sheet may store raw (unadjusted) DPS. Apply stock split/bonus adjustment (bonus issue / stock dividend) retroactively when presenting per-share data alongside EPS.
- **Label variants**: The same metric may appear under slightly different labels across Sheets (e.g., "ROIC (资本来源法)" vs "ROIC"). Use fuzzy matching or scan all labels before concluding a metric is absent.
