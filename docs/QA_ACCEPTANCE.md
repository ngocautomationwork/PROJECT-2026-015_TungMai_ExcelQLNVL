# QA Acceptance

## Required checks
- Sales import and Production import are correct and remain separate.
- Process plans are parsed correctly.
- Production material demand matches manual BOM x plan samples.
- Demand uses exact process plan date.
- CODE/CODE2...CODE6 aggregation is correct.
- New mapped code works without formula/script redesign.
- Warehouse inclusion/exclusion is explicit and testable.
- ON WAY is applied by date and Material Group.
- Projected stock = Opening + OnWay - Demand for both scenarios.
- Shortage date/quantity is correct.
- Re-importing the same source is idempotent.
- New weekly source does not erase required history.
- New product + BOM can enter calculation through master data.
- No solution-level #REF!, circular reference, or hard-coded local path.

## Evidence
VERIFY_LOG.md, CHANGELOG.md, QA.md, QA.pdf and screenshots for refresh, mapping, demand, stock, shortage and add-new-code/product cases.

## Gate
Self-QA Critical=0 and High=0 before Independent QA. CUSTOMER READY requires Owner/Independent QA confirmation.
