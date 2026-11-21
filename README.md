# Sierrathon PCB Design – Pocket Lab

KiCad 10 project for the Sierrathon PCB design hackathon.
Pocket Lab: a keyring-sized USB logic analyzer / voltmeter for engineering students.
Specs, BOM and scoring live in `Resources/`.

## Board outline (done)
- Rounded stick, 75 x 27 mm, 3 mm corners. USB-C on BOTH ends:
  J1 (left, silkscreen "PC") goes to the laptop, J2 (right, silkscreen "PROBE") goes to the circuit under test.
- M4 holes sit right behind each USB-C so the screws take plug-in force:
  MH1/MH3 at (13, 6)/(13, 21), MH2/MH4 at (62, 6)/(62, 21). MH4 doubles as the keyring hole.
  Coordinates are mm from the board's top-left (grid origin is set there).
- Blue drawings on `User.Comments` are placement guides only (J1, J2, RP2040 zone, LED zone,
  R20-R25 zone, hole circles). Delete them once parts are placed.

## Probe port J2 (judges approved adding a USB-C) - ON THE BOARD
J2 is the same USB-C part as J1 (Same Sky UJ20-C-H-G-SMT-TR, SeoHan's CUI_UJ20 footprint), at the right
end of the board, rotated 180 deg from J1. Each signal goes through a 1 kOhm series resistor (R20-R25,
same part as R7/R8) placed right behind J2.

| J2 pins | via | Net | RP2040 |
|---|---|---|---|
| A6 + B6 (D+, tied) | R20 | CH0 | GPIO9 (pin 12) |
| A7 + B7 (D-, tied) | R21 | CH1 | GPIO10 (pin 13) |
| A8 (SBU1) | R22 | CH2 | GPIO11 (pin 14) |
| B8 (SBU2) | R23 | CH3 | GPIO12 (pin 15) |
| A5 (CC1) | R24 | A0 | GPIO26 / ADC0 (pin 38) |
| B5 (CC2) | R25 | A1 | GPIO28 / ADC2 (pin 40) |
| GND pins + shell | | GND | |
| VBUS pins | | not connected (a charger/laptop plugged in here can't push 5 V in) | |

CH0-CH3 are 4 consecutive GPIOs (needed for the PIO logic-analyzer). GPIO0-3 / GPIO27 were the first
plan but there is no room to escape 4 vias under those 0.4 mm-pitch pins, so the channels moved to
GPIO9-12 and A1 to GPIO28 (both still analog-capable / consecutive). Drawn on the main sheet (top right)
with local labels; GPIO0-3 and GPIO27 keep their no-connect flags.

## Team rules for working in this repo
- KiCad files don't merge well. **Only one person edits a given file at a time**
  (`.kicad_sch` = schematic, `.kicad_pcb` = board). Say so in the team chat before you start.
- Before you open KiCad: `git pull`. When you're done: save, close KiCad, then commit and push.
- Never commit while the file is open and unsaved.

## Design rules (already set up – don't change without telling the team)
- 4 layers, 62 mil, 1 oz copper, FR-370HR, green mask / white silk (Board Setup > Physical Stackup).
- Constraints page floors are 4 mil clearance / 5 mil track **on purpose**: they must equal the
  90 Ω USB pair geometry (5 mil trace, 4 mil gap). Everything else is held to 6 mil by net
  classes and by `Sierrathon-PCB Design.kicad_dru` (Board Setup > Custom Rules).
- Net classes: `USB_90R` (USB_P/N, USBR_P/N, D_P/D_N – use the diff-pair router, key `6`),
  `Power` (VBUS, +3.3V, +1.1V, GND – 15 mil), `Default` (8 mil).
- Net names in the schematic must match the PDF exactly or nets won't land in the right class.

## Routing (done - DRC: 0 violations, 0 unconnected, schematic parity clean)
- USB (D_P/D_N, USB_P/USB_N, USBR_P/USBR_N) hand-routed and locked: 5 mil traces, 4 mil gap where
  the pair runs in parallel. USBR_N hops under USBR_P on B.Cu (one via pair) because the ESD chip's
  channel order is the reverse of the RP2040's USB_DM/USB_DP pin order.
- `diff_pair_uncoupled` limit is 7 mm (was 3 mm): each pair has to split around U4, R5/R6 and the swap;
  fine for full-speed USB (12 Mb/s).
- Remaining signals auto-routed with Freerouting (F.Cu/B.Cu only); In1.Cu = GND plane, In2.Cu = +3.3V
  plane (layer type "power", 8 mil plane clearance). GND/+3.3V pads drop to the planes through vias;
  RP2040 exposed pad has 6 thermal vias.
- Y1 uses an exact Abracon ABM8 land pattern (Libraries/Sierrathon.pretty, datasheet in Libraries/datasheets).

## Current status (3:05 PM) - SeoHan's schematic and footprints
- Project schematic = SeoHan's `finalschematic.kicad_sch` (only change: internal project name pointed at this project).
- J1, SW1, SW2, SW3, U3, Y1 use SeoHan's footprints (CUI_UJ20-C-H-G-SMT-TR, SON4_PTS526 SM15 SMTR2 LFS_CNK,
  D6R90_CNK, SOIC127P790X216-8N, XTAL_ABM8-272-T3), aligned pad-for-pad to the routed board. Small DFM edits:
  J1 signal-pin mask margin 0.102 -> 0.04 mm (keeps a mask web at 0.85 mm pitch), J1 silk past the board edge
  removed, duplicate "*" silk text on the PTS526 removed, U3 pin-1 silk dot that sat on copper removed.
- Rules: min annular ring 0.127 -> 0.12 mm (J1 pads have a 0.125 mm ring); USB_90R class + a 4 mil in-pair rule
  also cover the auto-named Net-(U1-USB_DP) / Net-(U1-USB_DM) nets.
- DRC --severity-all --schematic-parity: 0 violations, 0 unconnected, 0 footprint errors.
- Known schematic items left as-is for now: USBR_P/USBR_N and Led5 net names, BOM values on J1/MH1-4/SW3/U2,
  most parts have no MPN/Manufacturer fields, ERC warnings from downloaded symbols' pin types.
- My earlier footprints (SW_CK_*, USB_C_Receptacle_SameSky_*, Crystal_SMD_Abracon_ABM8-*) stay in the library, unused.
