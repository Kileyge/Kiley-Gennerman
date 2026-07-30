# Prompt Log

## 2026-07-30 — Stage 2 Scenario 4 specification

**Prompt used:** “Draft a 2–3 page technical specification for Scenario 4: a U.S. aerospace manufacturer with a €20,000,000 EUR receivable due in one year. Follow the Stage 2 requirements: standardized named ranges, tab architecture, forward/money-market/put-option calculation flow, sensitivity plan, exact gray outputs, and validation checks. Mark all market values as indicative pending Stage 4 live data.”

**Iteration recorded:** The first draft described the money-market hedge as a generic EUR borrowing/investment sequence and did not set an audit threshold. I corrected it before finalizing the spec.

- **Before:** “Compare the money-market result with the forward for parity.”
- **After:** `EUR_BORROW = FC_AMT / DF_FC`; convert at `S0_in`; invest at `DF_USD`; calculate `PARITY_GAP = USD_MM − USD_FWD`; require `ABS(PARITY_GAP) <= 1.00` using unrounded values.

This change makes the workbook formula sequence and acceptance criterion reproducible for the Stage 3 builder.
