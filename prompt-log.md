# Prompt Log

## 2026-07-30 — Stage 2 Scenario 4 specification

**Prompt used:** “Draft a 2–3 page technical specification for Scenario 4: a U.S. aerospace manufacturer with a €20,000,000 EUR receivable due in one year. Follow the Stage 2 requirements: standardized named ranges, tab architecture, forward/money-market/put-option calculation flow, sensitivity plan, exact gray outputs, and validation checks. Mark all market values as indicative pending Stage 4 live data.”

**Iteration recorded:** The first draft described the money-market hedge as a generic EUR borrowing/investment sequence and did not set an audit threshold. I corrected it before finalizing the spec.

- **Before:** “Compare the money-market result with the forward for parity.”
- **After:** `EUR_BORROW = FC_AMT / DF_FC`; convert at `S0_in`; invest at `DF_USD`; calculate `PARITY_GAP = USD_MM − USD_FWD`; require `ABS(PARITY_GAP) <= 1.00` using unrounded values.

This change makes the workbook formula sequence and acceptance criterion reproducible for the Stage 3 builder.

## 2026-07-30 — Stage 3 workbook build and audit

**Prompt used:** “Build an Excel workbook exactly from `2026-07-30-gennerman-scenario4-eur-receivable-spec.md`. Include every required named range, all specified tabs, formula-driven forward, three-step money-market, put and call calculations, eleven-row sensitivity table and line chart, and visible validation checks. Apply the specified yellow/blue/green/gray convention.”

**Specification correction before regeneration:** The original indicative forward placeholder of `1.0750` was inconsistent with the model’s own ACT/360 money-market assumptions and caused the required parity test to fail. It was corrected to `1.1273` (the rounded covered-interest-parity equivalent) in the specification; Stage 4 will still replace it with a matching-tenor live forward quote.

**Audit outcomes:** Corrected the forward’s internal precision so parity passes within the $1 tolerance; repaired the put-floor sensitivity link and the Cover status link; and converted displayed formula notes to literal text so the audit trail remains readable. The final formula-error scan returned no errors and all live checks pass.

**Rubric-completeness update:** Added a formula-driven call-reference schedule linked to each `S_T` scenario, so the call’s premium-backed payoff and USD outlay are directly inspectable rather than shown only at the base case.

**Submission integration update:** Added an Audit Summary workbook tab that mirrors the required audit findings and links its status fields directly to the live Checks tab, so the workbook and written audit can be reviewed together.

**Audit-note rewrite:** Reorganized the written audit into a concise, evidence-led record that matches the Audit Summary tab: five specific findings, corrections, final control outcomes, and the Stage 4 handoff.

## 2026-08-02 — Stage 4 market-data population

**Prompt used:** “Populate the Scenario 4 EUR receivable workbook with dated, reputable EUR/USD spot and reference-rate data; compute a CIP-implied one-year forward if an executable free forward quote is unavailable; retain the assignment option premia; document sources, dates, proxy logic, and validation results.”

**Outcome:** Loaded ECB EUR/USD reference spot (1.1389), FRED 1-year U.S. Treasury CMT (4.06%), and the ECB deposit facility proxy (2.25%). The one-year forward was computed as 1.1593342407 under the workbook’s ACT/360 CIP formula. The model checks passed after population; full provenance and the lab-access limitation are recorded in `data/2026-08-02-gennerman-market-data.md`.
