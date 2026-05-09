# Magic DRC errors with sky130 res_xhigh_po and cap_mim_m3_1 cells in TT analog 2x2

**Repo:** https://github.com/thomasgilbert481/tt_um_thomas_ep_sensor
**Failing run:** https://github.com/thomasgilbert481/tt_um_thomas_ep_sensor/actions/runs/25589707861
**Magic version in CI:** 8.3 revision 568
**PDK:** sky130A (commit `0536d02d875c8f67dd7cca3902ac457e62f20005` — TT current)

## Summary

99 Magic DRC errors (= 24 unique violations × ~4 edges each). All come from
sky130 foundry **resistor and MIM-cap gencell** instances. Errors persist even
when:

- KLayout DRC (FEOL/BEOL/offgrid) is **clean** (0/0/0)
- Boundary, Pin, Power-pin, Layer, Cell-name, urpm/nwell, Analog-pin
  checks all pass
- I add wide GDS URPM markers (79/20) over the resistor cells (no effect
  because magic computes URPM from `bloat-all uhrpoly xpc` poly-derived,
  per `sky130A.tech` lines around `layer URPM`)

The base foundry cells (`res_xhigh_po_0p35`, `cap_mim_m3_1`) are individually
DRC-clean when loaded standalone. The errors only appear in our chip's
**magic-customized variants** (cells like `sky130_fd_pr__res_xhigh_po_0p35_YR6MM7`
which are gencell-generated for our specific R values).

## Errors by category

| Rule | Count (CI / local) | Source | Coords (sample) |
|---|---:|---|---|
| `Width of RPM/URPM < 1.27um (rpm.1)` | 22 / ~5 | All 6 `res_xhigh_po_0p35_*` instances | `(141.55, 155.88)–(142.07, 157.28)` etc. |
| `MiM cap spacing to via2 < 0.1um (capm.8)` | 8 / 2 | `cap_mim_m3_1_UBRWDH` cell | `(159.06, 50.06)–(161.04, 50.50)` |
| `MiM cap spacing to unrelated metal3 < 1.34um (capm.11)` | 70 / 17 | `cap_mim_m3_1_XCEES9`, `cap_mim_m3_1_CRXE5C` | `(275.20, 190.66)–(275.50, 198.51)` etc. |

## Verified

I deleted the 6 res cells + 3 conflicting MIM caps from the GDS — Magic DRC
drops to **0**. So I know exactly which 9 cell instances are the source.

## Constraints

- **Cannot consume more analog pins** for external bias (analog_pins is at
  2 already; ua[2..5] are needed for chip features)
- The chip is a 2.7 GHz EP sensor with on-chip bias generation
  (`Vbmid=1.5V`, `Vbn=1.20V`) via 6 `res_xhigh_po_0p35` resistors and 3
  decap MIM caps

## Asks

1. Is there a **known fix** for sky130 `res_xhigh_po` gencell rpm.1 errors
   under magic 8.3.568? (older magic apparently passed these.)
2. Is there a **waiver mechanism** for foundry-cell-internal DRC errors
   (analogous to the SRAM_EXCLUDE / `sky130_fd_io__*` exclusions in
   `sky130A_mr.drc` for KLayout)?
3. Does TT recommend a **different resistor model** for analog 2x2 bias
   networks that reliably passes Magic DRC?
4. Is **regenerating the gencells** with the current magic version expected
   to produce clean output? If so, what's the recommended flow?

Happy to share the GDS/LEF artifacts directly if helpful.
