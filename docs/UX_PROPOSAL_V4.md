# UX proposal V4 — Simulation-first (awaiting Owner approval)

Correction of V3 direction. Not a scope CR. No business-rule changes.
Do not implement the full engine until Owner approves this UX.

## 1. Confirmation: Simulation is the customer file

| Role | File | Action |
|---|---|---|
| Customer product | `Simulation Coway, KyungDong_Update sale W35 (1).xlsb` | Working copy is the delivery |
| Raw backup | same XLSB, never edited | Rollback source |
| Sales source | `W36_2026 Sales plan_*.xlsx` and later weekly files | Inbox, not the product |
| Production source | `COWAY PRODUCTION PLAN *.xlsx` | Inbox, not the product |
| Stock source | `(Q) Tra cứu tồn kho theo từng kho hàng *.xls` | Inbox snapshot |
| Stop using as customer product | `PROJECT-2026-015_TungMai_ExcelQLNVL_MASTER.xlsx` | Internal only if needed |
| Stop using as customer product | `TungMai_Excel_Quan_Ly_NVL_2026_GiaoKhach.xlsx` | Internal only if needed |

Goal: the old customer file, made smarter. Customer does not learn a new system.

Visible sheets stay the Simulation tabs:

- `Packing` / `ASSY` / `AI` / `SMD` — main 5-row grid (SALE / BALANCE / Produce / Delivery plan / STOCK)
- `ON WAY` — NCC by date
- `COWAY BOM` — CODE / CODE2…CODE6 on the same row
- `SALE PLAN`, `STOCK DAY`

Not shown to the customer: `CONTROL`, `MASTER__`, `DATA__`, `DEMAND__`, `STOCK__`, `MaterialGroup MG-...` as the primary code.

## 2. Import flow (Excel-native, Refresh All)

Customer steps:

1. Drop the 3 weekly/monthly source files into a fixed inbox folder.
2. Open the Simulation WORKING workbook (keep Excel open).
3. `Data > Refresh All`.
4. Stay on `Packing` / `ASSY` / `AI` / `SMD` and read code, BOM, Sales, KHSX, NCC, stock, shortage.

No CMD, no PowerShell, no close-Excel-to-run-script.

### Folder (pattern, not hard-coded W36 / 20260827)

```
QLNVL_2026/
  Simulation_WORKING.xlsx          <- customer opens this
  Nguon/
    01_Sales/                      <- *Sales plan*.xlsx
    02_KHSX/                       <- *PRODUCTION PLAN*.xlsx
    03_TonKho/                     <- *ton kho*.xls / *.xlsx
```

Source root is a named Excel parameter (`SourceRoot`) stored on hidden `_CFG`.
Queries use `Folder.Files` + name pattern + latest `Date modified`.
Next week the customer drops a new file; they do not edit query names.

Fallback if Folder connector is blocked (old Excel / trust center):
three stable filenames in `Nguon/` (`Sales.xlsx`, `KHSX.xlsx`, `TonKho.xls`) that the customer overwrites. Still Refresh All. Still no W36 hard-code.

### Power Query jobs

| Query | Source | Transform | History rule |
|---|---|---|---|
| `src_Sales` | latest Sales file | unpivot weeks; map each week to the Thursday inside the bucket (Fri–Thu → Thursday). Example: 21–27/08/2026 → 27/08; 28/08–03/09 → 03/09 | replace weeks present in the new file; keep weeks absent (annual history); distinct on SKU + Thursday |
| `src_Prod` | latest Production file | keep exact process date; keep process/BOM key | replace rows present for (FG, process, date); keep the rest; no date shift |
| `src_Stock` | latest Stock snapshot | usable warehouses only after confirmed codes | replace that snapshot period; keep prior months |
| `src_BOM` | `COWAY BOM` in this workbook | CODE…CODE6 = one material; stock summed | new code added on the same row continues to calculate |

Sales and Production stay two parallel scenarios. They do not overwrite each other.
STOCK / BALANCE formulas stay the Simulation logic. This proposal does not invent a new shortage formula.

### Locked / open source file

Power Query reads a memory copy (`File.Contents`). If Windows still locks the file, show a Vietnamese note on the visible banner: close the source workbook or copy it into `Nguon/` first. No technical OLEDB dialog as the primary path. No terminal.

### Hidden backend (Very Hidden)

`_CFG`, `_HIST_SALES`, `_HIST_PROD`, `_HIST_STOCK`, `_PQ_*`
Customer never uses these to run the business.

## 3. UX mockup (legacy layout, thin addition only)

The green `Data > Refresh All` control in the HTML mockup is Excel's native ribbon command. We are not adding a custom toolbar.

Added on the existing title row of the Simulation sheet, not a dashboard:

- One-line instruction: drop files -> Refresh All
- Tiny status: last Sales / KHSX / Stock file names and last refresh time

Unchanged:

- Left: MODEL, CODE2, CODE, ITEM, Description, process, vendor, SHORT
- Five rows per material: SALE, BALANCE, Produce, Delivery plan, STOCK
- Date columns across, weekends in red
- Display codes are real item codes, not `MG-...`

## Open items (no assumption)

1. Production `TSMAPCW0151V` / `(BAS37-D)` / process `PK` / qty 2,000 / 06/10/2026: audit raw XLSB including hidden rows/columns before any `MISSING_BOM` conclusion. If still absent, Owner asks the customer.
2. Sales `TSMAPCW0121K` qty 1,000: do not drop on a K/service guess. Need source or business-rule evidence; otherwise Owner asks the customer.

## Gate

Owner approves this UX → then copy the real XLSB to WORKING and implement queries.
Until then: no full build, no V3 MASTER/GiaoKhach path.
