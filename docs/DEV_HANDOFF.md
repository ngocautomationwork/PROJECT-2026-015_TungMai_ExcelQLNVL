# Dev Handoff

## Locked business rules
1. Sales Plan and Production Plan remain separate scenarios.
2. Production demand is calculated per process and on the exact process plan date; no automatic lead-time shift.
3. CODE/CODE2...CODE6 on the same mapping row belong to one Material Group and stock is aggregated.
4. New material codes must be recognized via mapping without rewriting the solution.
5. Beginning stock comes from the system snapshot; usable warehouses are summed while NG and repair/rework warehouses are excluded after exact codes/names are confirmed.
6. Supplier ON WAY is date-based and old/new codes map to Material Group.
7. Weekly source files may remove prior weeks; the annual master must preserve required history and repeated refresh must not duplicate.
8. One working master must support the year and controlled addition of new products/materials/BOM/codes.

## Source audit requirements
- Audit raw Sales, Production, Stock and Simulation XLSB in protected Drive/ZIP.
- Google Production source copy contains formula `#ERROR!` caused by conversion/external-link compatibility; compare against raw `.xlsx` before drawing conclusions.
- Simulation XLSB is not directly convertible by the Drive importer; audit the raw XLSB locally and populate validated MASTER_MATERIAL / MASTER_BOM.
- Chat calls a process Packing while source sheet is named Parking; do not rename by assumption.
- Exact NG / repair warehouse codes are still an implementation confirmation item.

## Required deliverables
- SOURCE_AUDIT.md
- DATA_MODEL.md
- OPEN_QUESTIONS.md
- CHANGELOG.md
- VERIFY_LOG.md
- QA.md
- QA.pdf
- HDSD.pdf
- screenshots evidence
- updated DEV WORKING Google Sheet / UAT copy

Developer may report `READY_FOR_INDEPENDENT_QA` only after self-QA Critical=0 and High=0. Developer must not report CUSTOMER READY.
