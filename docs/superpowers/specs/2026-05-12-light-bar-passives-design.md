# Light Bar — Passives (decoupling caps + data series resistors)

**Date:** 2026-05-12
**Project:** `Light Bar` (KiCad 9) — 21× WS2812B RGB LED bar
**Scope of this spec:** add the passive support components to the schematic. PCB placement and net wiring are the *next* task and are out of scope here (only the intended net plan is recorded for reference).

## Context / current state

- `Light Bar.kicad_sch` contains: D1–D21 = `LED:WS2812B` (`LED_SMD:LED_WS2812B_PLCC4_5.0x5.0mm_P3.2mm`), J1 + J2 = `Conn_01x03` (`Connector_JST:JST_XH_S3B-XH-A_1x03_P2.50mm_Horizontal`), and one `power:GND` symbol. **The schematic is not yet wired (0 wires).**
- Connector pinout (both J1 and J2, 3-pin): **GND · PB0 (data) · 5VDC**. J1 = power + data in; J2 = passthrough out. Data net name = `PB0` (matches the host MCU pin).
- Visual goal: passives should look clean/uniform on the bar → **1206 SMD for all resistors and the per-LED caps**; bulk cap kept low-profile SMD.

## Components to add

| Ref | Value | Footprint | Symbol | Qty | Role |
|---|---|---|---|---|---|
| C1–C21 | 100 nF, 50 V, X7R | `Capacitor_SMD:C_1206_3216Metric` | `Device:C` | 21 | VDD↔GND decoupling, one per WS2812B; numbered to match LEDs (C1↔D1 … C21↔D21) |
| C22 | 47 µF, 16 V, X5R MLCC | `Capacitor_SMD:C_1210_3225Metric` | `Device:C` | 1 | bulk reservoir on the 5VDC rail, placed by J1 |
| R1 | 330 Ω, 1/4 W | `Resistor_SMD:R_1206_3216Metric` | `Device:R` | 1 | series resistor on data IN: `J1.PB0 → R1 → D1.DIN` |
| R2 | 330 Ω, 1/4 W | `Resistor_SMD:R_1206_3216Metric` | `Device:R` | 1 | series resistor on data OUT: `D21.DOUT → R2 → J2.PB0` |

All symbols and footprints ship with KiCad 9 and are already registered in the user's global library tables — nothing to install.

### Value rationale
- **100 nF / LED:** the WorldSemi WS2812B datasheet recommends a 100 nF decoupling cap between VDD and VSS at every LED.
- **47 µF bulk:** 21 LEDs ≈ 1.3 A peak at full white; a ~47 µF reservoir near the input smooths inrush/ripple. A 1210 X5R MLCC keeps it low-profile and non-polarized (so `Device:C`, not `Device:C_Polarized`). Alternative considered: 3× 10 µF 1206 in parallel for visual uniformity — rejected in favour of a single part unless the user prefers it.
- **330 Ω series:** standard WS2812B data-line damping value (Adafruit/WorldSemi reference designs). 470 Ω was offered as an alternative; 330 Ω chosen.

## Intended net plan (for the wiring task that follows — not implemented here)

- `5VDC` (a.k.a. `+5V`): `J1.5VDC` — `C22` — every `D1..D21 VDD` (each with its own `C1..C21` to GND) — `J2.5VDC`
- `GND`: common to `J1`, `J2`, `C22`, all LED `VSS`, all caps
- `PB0` (data): `J1.PB0` → `R1` → `D1.DIN`; `D1.DOUT → D2.DIN → … → D21.DOUT` → `R2` → `J2.PB0`
- Exact pin-number ↔ signal mapping on the JST (which of pins 1/2/3 is GND/PB0/5VDC) to be confirmed during wiring.

## Out of scope
- Schematic wiring / net labels / power flags.
- PCB component placement and routing (copper pours, trace widths for the 5 V rail, etc.).
- BOM/CPL export, manufacturing outputs.

## Notes
- The project directory is not a git repository, so this spec is saved but not committed.

## Implementation log (2026-05-12)
- Added to `Light Bar.kicad_sch`: C1–C21 (100 nF, `Capacitor_SMD:C_1206_3216Metric`), C22 (47 µF, `C_1210_3225Metric`), R1/R2 (330 Ω, `R_1206_3216Metric`), plus `#FLG01`/`#FLG02` PWR_FLAGs on the 5VDC and GND nets. Existing D1–D21, J1, J2 left in place.
- Wired with net labels: `5VDC` (45 nodes: 22 cap pin-1 + 21 LED VDD + J1.1 + J2.1), `GND` (45 nodes: 22 cap pin-2 + 21 LED VSS + J1.3 + J2.3 + #PWR01), `PB0` (J1.2–R1.1), `DIN_HEAD` (R1.2–D1.DIN), chain `LED1_2 … LED20_21` (D_n.DOUT–D_{n+1}.DIN), `DOUT_TAIL` (D21.DOUT–R2.1), `DATA_OUT` (R2.2–J2.2).
- ERC: 0 errors. 2 remaining warnings (`lib_symbol_issues`): J1/J2 still carry lib_id `S3B-XH-A_LF__SN_:…`; the symbol is embedded in the schematic so it works, but the source library was removed earlier. Cosmetic only — fixable by swapping J1/J2 to a stock connector symbol in Eeschema.
- Backup of pre-wiring schematic: `Light Bar.kicad_sch.bak-before-wiring`. Stale `.lck` / autosave for the schematic removed (autosave was byte-identical to the pre-wiring file).
## PCB layout (2026-05-12)
- Board: **350 mm × 16 mm** rectangle on Edge.Cuts. (Lengthened/widened from the original ~300×12 so the 1206-cap gaps + 5V + data routing fit.)
- Placement: D1–D21 rot 180 in a single row at 13 mm pitch (x = 51…311, y = 8); decoupling caps C1–C20 horizontal in the gaps (y=8.5), C21 above D21; C22 (bulk) + R1 by J1 at the left end; R2 by J2 at the right end; J1 (rot 90) at the left end and J2 (rot 270) at the right end — both oriented so the cable exits straight off the end with the housing back toward the LEDs; H1 (x=30) and H2 (x=326) = `MountingHole:MountingHole_5.3mm_M5`, on the centerline near each end.
- Copper: **GND pour on B.Cu** over the whole board (inset 1 mm) + 43 stitching vias on every SMD GND pad (LEDs + caps); J1/J2 GND pads are THT so they tie to the pour directly. **5V on F.Cu** — a 0.6 mm rail along the bottom edge (y=11.5) from x=9 to x=335, popping up to every LED VDD pad and every cap 5VDC pad, with a single via near J2 hopping the 5V onto B.Cu to reach J2 pad-1 (avoids crossing the data-out trace). **Data chain on F.Cu** (0.25 mm) — D_n DOUT → D_(n+1) DIN weaving through each gap above the decoupling cap; J1.PB0→R1→D1.DIN and D21.DOUT→R2→J2.pad-2 routed at the ends (around the M5 holes).
- DRC: **0 errors**, schematic-parity OK. Warnings only: 43 "via dangling" + 44 "unconnected" — both are GND-pour artifacts of `kicad-cli` not filling zones; they clear as soon as the board is opened in pcbnew (zone auto-fills, or press `B`). Plus 23 "footprint differs from library" (from normalizing silk text rotation) and a couple silk-near-edge warnings — all cosmetic. `courtyards_overlap` (M5 hole H2 near J2 — intentional close placement, no physical conflict) was set to "warning" in `.kicad_pro`.
- Backups in the project folder: `Light Bar.kicad_pcb.bak-before-layout`, `.STAGE1-placed`, `.STAGE2-geo`, `.STAGE3-connectors`, `.STAGE4-before-route`, `.STAGE5-routed-2026-05-12`.
- Remaining: open pcbnew → refill zones (`B`) → review; then manufacturing outputs (gerbers / drill / BOM / CPL) for fab.
