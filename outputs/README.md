# Sierrathon - Pocket Lab: submission package

Generated with KiCad 10.0.7 from the files in this repo.

| Folder / file | What it is |
|---|---|
| `Sierrathon-PCB-fabrication.zip` | Everything a PCB fab needs, zipped: the Gerbers + drill files below |
| `fabrication/*.gtl .g1 .g2 .gbl` | Copper L1 (top), L2 In1 (GND plane), L3 In2 (+3.3V plane), L4 (bottom) |
| `fabrication/*.gts .gbs / .gto .gbo / .gtp .gbp` | Solder mask (green), silkscreen (white), paste - top / bottom |
| `fabrication/*.gm1` | Board outline (75 x 27 mm) |
| `fabrication/*-PTH.drl, *-NPTH.drl, *drl_map.pdf` | Excellon drill files (mm) + drill maps; min drill 0.203 mm (8 mil) |
| `fabrication/*-job.gbrjob` | Gerber job file (stack-up / layer order for the fab) |
| `assembly/*-BOM.csv` | Bill of materials (grouped) |
| `assembly/*-positions.csv` | Pick-and-place, both sides, mm |
| `documentation/*-schematic.pdf` | Schematic |
| `documentation/*-pcb-layers.pdf` | One page per copper/silk/fab layer |
| `documentation/*-3D-top.png, *-3D-bottom.png` | 3D renders |
| `reports/DRC.rpt` | DRC, all severities, with schematic parity: 0 violations, 0 unconnected, 0 parity issues |
| `reports/ERC.rpt` | ERC of the schematic (20 items, mostly pin-type warnings from downloaded symbols) |

Board: 4 layers, FR-370HR, 62 mil, 1 oz copper, green mask, white silkscreen, IPC Class 2,
through-hole vias only, 90-ohm USB pairs (5 mil / 4 mil) on L1 referenced to the L2 GND plane.
