![](../../workflows/gds/badge.svg) ![](../../workflows/docs/badge.svg)

# tt_um_thomas_ep_sensor — Chiral Exceptional-Point Sensor (2.7 GHz)

A fully-analog Tiny Tapeout chip implementing Zhao's chiral
exceptional-point (EP) sensor topology in Sky130A.  Two on-chip 130 × 130 µm
square spirals (L = 1.351 nH, Q ≈ 15) form matched LC tanks at ~2.7 GHz,
coupled in one direction through an NMOS source follower and a coupling MIM
cap.  An 8-bit binary-weighted cap-bank (cv-array) perturbs the V1 tank;
the V2 spectrum's peak splitting Δf scales as **√ε** — the canonical EP
signature, giving the sensor a square-root-rather-than-linear sensitivity
gain over a conventional reciprocal coupling.

| Block | Specification |
|-------|---------------|
| Operating frequency | ~2.71 GHz (Zhao-exact κ = 0.3125) |
| Tank L | 1.351 nH (FastHenry-extracted, m4+m3+m2 stack) |
| Tank Q | ≈ 15 |
| EP slope | 0.502 (Monte-Carlo verified, 500 / 500 trials in [0.45, 0.55]) |
| cv-array | 8-bit binary, 20 fF LSB to 1280 fF MSB |
| Supply | VDPWR = 1.8 V (no 3.3 V) |
| Tile size | 2 × 2 |
| Analog pins | ua[0] = V1, ua[1] = V2 |
| Digital inputs | ui_in[7:0] = cv-array bits b0..b7 |

- [Read the project datasheet](docs/info.md)

## What is Tiny Tapeout?

Tiny Tapeout is an educational project that aims to make it easier and cheaper than ever to get your digital designs manufactured on a real chip.

To learn more and get started, visit https://tinytapeout.com.

## Verification status

* **DRC** — KLayout sky130A_mr.drc deck (TT precheck-equivalent), `feol=true / beol=true / offgrid=true` runs all return 0 violations.
* **LVS** — magic + netgen extraction, 225 devices match between layout and schematic.
* **Monte Carlo** — 500 trials, ±5 % cap variation, ±10 % L variation, ±50 mV bias variation — 100 % yield in the [0.45, 0.55] target slope band.
* **Post-layout robustness** — schematic AC sweep with realistic lumped parasitics (sized to bound the layout-extracted parasitic delta of +118 fF total) shows EP scaling preserved with sub-1 % uniform frequency offset.

## Resources

- [FAQ](https://tinytapeout.com/faq/)
- [Analog specs](https://tinytapeout.com/specs/analog/)
- [Join the Tiny Tapeout community](https://tinytapeout.com/discord)

## What next?

- [Submit your design to the next shuttle](https://app.tinytapeout.com/).
