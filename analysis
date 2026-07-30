# Stage 3 — Build Audit Note
**Scenario 4 · EUR Receivable FX Hedge Model**
Author: Kiley Gennerman · Date: 2026-07-30
Workbook audited: `2026-07-30-gennerman-scenario4-eur-receivable-model.xlsx` (as generated)
Audit tool: `audit_check.py` (mechanical contract check, included alongside this note)

---

## Finding 1 — Named ranges were unanchored, causing 65 formula errors (FAIL → FIXED)

**What I checked:** Ran `scripts/recalc.py` on the generated workbook to force LibreOffice to
evaluate every formula and cache real values, then loaded the cached values back with
`openpyxl`.

**What I found:** 65 of 205 formulas returned `#VALUE!`, cascading from `Forward!B9` through
`Money Market`, `Options`, and most of the `Sensitivity` grid. Inspecting the raw XML
(`xl/workbook.xml`) showed every one of the ten contract named ranges — `FC_AMT`, `S0_in`,
`F0_in`, etc. — was defined without an absolute `$` anchor, e.g. `FC_AMT` = `Inputs!B6` instead
of `Inputs!$B$6`. An unanchored defined name is stored relative to the cell it was defined from;
when a formula elsewhere in the workbook references that name, the reference shifts by the
offset between the formula's own cell and the name's home cell. This produced silently wrong
targets, not just errors — e.g. `Options!B6` (`=S0_in`) evaluated to the **text label**
`"USD per EUR"` (pulled from the adjacent `C6` cell) instead of the numeric spot rate `1.1`,
because the reference had drifted one column over.

**What I did:** Rewrote all 32 defined names to absolute references (`Inputs!$B$6`, `'Money
Market'!$B$7`, etc.) via `openpyxl`'s `defined_names[name].attr_text`, then re-ran `recalc.py`.
Result: `0` errors across `216` formulas (the extra 11 formulas come from Finding 2). Every
downstream value — `USD_FWD`, the money-market three-step build, the put premium/payoff, and
all eleven sensitivity rows — now recalculates correctly. This is exactly the class of defect
the assignment brief calls out ("a named range attached to the wrong cell") — here it was
attached to the right *label* but wrong *anchor type*, which is a more dangerous variant because
it doesn't break every formula, only the ones evaluated from an offset cell.

## Finding 2 — Sensitivity Results table was missing the required `HEDGE_VALUE_PUT` column (FAIL → FIXED)

**What I checked:** Compared the `Sensitivity` tab's actual column headers (row 12) against
spec §6, which requires exactly: `S_T, USD_NO_HEDGE, USD_FWD, USD_MM, USD_PUT,
HEDGE_VALUE_FWD, HEDGE_VALUE_MM, HEDGE_VALUE_PUT, OVERALL_WINNER`.

**What I found:** The generated table had only 8 of the 9 required output columns —
`HEDGE_VALUE_PUT` was skipped entirely, with `OVERALL_WINNER` sitting in the column
`HEDGE_VALUE_PUT` should have occupied (column I). `OVERALL_WINNER` itself still computed
correctly (it compares `USD_NO_HEDGE`/`USD_FWD`/`USD_MM`/`USD_PUT` directly, not the hedge-value
columns), so this wasn't visible as a formula error — only as a missing column, which the
mechanical contract check catches but a glance at "does it calculate" would not.

**What I did:** Inserted a `HEDGE_VALUE_PUT` column at I (`=F{row}-C{row}`, matching the pattern
of `HEDGE_VALUE_FWD`/`HEDGE_VALUE_MM`), copied its formatting from `HEDGE_VALUE_MM`, and moved
`OVERALL_WINNER` to column J. Confirmed the line chart's series ranges (`Sensitivity!$B$13:$F$23`
only) were unaffected by the insertion before saving.

## Finding 3 — Confirmed: parity, hedge invariance, and put continuity all pass on recalculated values

**What I checked:** After fixing Findings 1–2, re-ran spec §7's validation rules against the
recalculated cached values:
- `PARITY_CHECK`: `ABS(USD_MM − USD_FWD) <= $1`
- `USD_FWD` and `USD_MM` constant across all 11 sensitivity rows
- `USD_NO_HEDGE` step size equals `FC_AMT × ΔS_T`
- `USD_PUT` continuity at `S_T = K_PUT`

**What I found (all pass, with numbers):**
- `USD_FWD = USD_MM = $22,546,555.95`; `PARITY_GAP = 8.6e-08`, far inside the $1 tolerance —
  confirming the indicative `F0_in` (1.1273278) really was back-solved from covered interest
  parity as the spec claims, not just asserted.
- Both `USD_FWD` and `USD_MM` are flat across all 11 rows (`MAX − MIN = 0`).
- Row-to-row `USD_NO_HEDGE` step = `$220,000` = `FC_AMT × S0_in × STEP_FRAC` exactly.
- At `S_T = K_PUT = 1.10` (the baseline row, n=0), `USD_PUT = $21,372,625 = K_PUT × FC_AMT −
  FV_PREMIUM_PUT`. Below the strike it stays flat at that floor; above the strike it rises
  1-for-1 with `S_T` (e.g. n=+1: `$21,592,625`, a `$220,000` step matching `USD_NO_HEDGE`'s
  step, less zero incremental premium change) — the floor-with-upside shape the spec describes.

No further findings — I did not find additional contract violations after these three areas
were checked; I'm not claiming the workbook is otherwise flawless, only that these were the
areas the spec's own validation rules and the mechanical contract check exercise.

---

## Contract-compliance summary (post-fix, via `audit_check.py`)

| Check | Result |
|---|---|
| All 10 named ranges anchored to the correct cell | 10/10 PASS |
| All 7 required tabs present | 7/7 PASS |
| Sensitivity table has all 9 required output columns | 9/9 PASS |
| Sampled calculated cells are formulas, not hardcoded | 20/20 PASS |
| §7 validation rules (parity, invariance, no-hedge step) | 4/4 PASS |
| **Total** | **50/50 PASS**, 0 formula errors across 216 formulas |

## Files
- Fixed workbook: `2026-07-30-gennerman-scenario4-eur-receivable-model.xlsx`
- Audit script (reusable, run it against any revision): `audit_check.py`
