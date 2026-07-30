# Stage 3 Build Audit — Scenario 4 EUR Receivable Hedge Model

| Field | Value |
|---|---|
| **Workbook audited** | `models/builds/2026-07-30-gennerman-scenario4-eur-receivable-model.xlsx` |
| **Auditor** | Kiley Gennerman |
| **Date** | 2026-07-30 |
| **Build basis** | `docs/specs/2026-07-30-gennerman-scenario4-eur-receivable-spec.md` |
| **Result** | PASS after corrections below |

## Audit approach

I inspected the workbook structure, the defined-name list, representative formula chains, the eleven-row sensitivity grid, live check figures, and rendered views of every worksheet. The final formula-error scan found no `#REF!`, `#DIV/0!`, `#VALUE!`, `#NAME?`, or `#N/A` cells.

## Findings and corrections

| # | What I checked | What I found | What I did | Final evidence |
|---:|---|---|---|---|
| 1 | Forward / money-market parity against the stated $1 tolerance | The original indicative `F0_in` placeholder (1.0750) was not consistent with the displayed ACT/360 USD and EUR rates. It produced a material parity gap and a failing check. | Corrected the indicative forward placeholder in the Stage 2 spec and Inputs tab to the full covered-interest-parity value, 1.127327797440784 (displayed as 1.1273). | `PARITY_GAP` is effectively $0; `PARITY_CHECK` and the Checks-tab parity control both show PASS. |
| 2 | Put-floor output trace from Options to the sensitivity grid | The first draft linked the put-floor formula to the money-market column / wrong row range instead of the `USD_PUT` sensitivity results. | Repointed `USD_PUT_FLOOR` to `MIN('Sensitivity'!F13:F23)` and corrected the displayed formula note. | Options shows a $21,372,625 put floor, matching the lowest `USD_PUT` result in the grid. |
| 3 | Cover-page model-status link | The Cover tab referenced the wrong checks-cell address, so it could not reliably surface overall status. | Relinked the Cover status cell to `='Checks'!F13`. | Cover displays PASS, matching the Checks tab’s overall model status. |
| 4 | Rendered calculation/audit-note columns | Formula descriptions beginning with `=` were being treated as live formulas and displayed evaluated values rather than the intended audit text. | Converted description entries to literal text while keeping the actual calculations in formula cells. | Forward, Money Market, and Options tabs now display readable formula logic; their calculation values remain formula-driven. |

## Final validation results

- All ten required named ranges (`FC_AMT`, `S0_in`, `F0_in`, `R_USD`, `R_FC`, `K_PUT`, `K_CALL`, `PREM_PUT`, `PREM_CALL`, and `T_DAYS`) resolve to the intended Inputs-tab cells.
- The forward, three-step money-market, put, call-reference, sensitivity, and check cells are formulas—not pasted output values.
- The sensitivity grid is formula-driven from −5% to +5% in 1% steps and includes the required comparison chart.
- The visible checks for parity, forward constancy, money-market constancy, no-hedge step, and put-base tie-out all pass.
- The workbook uses the required legend and applied color convention: yellow inputs, blue assumptions, green formulas/passing controls, and gray outputs.

## Stage 4 handoff

Before using this model for a decision, replace the yellow indicative inputs with same-date, matching-tenor live data and rerun the Checks tab. Any failed parity control should be investigated for rate, tenor, date, or day-count mismatch before recommendation.
