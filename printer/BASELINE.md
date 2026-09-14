# OpenPrinter Ender 5 Pro Baseline

Printer:

* Ender 5 Pro
* Creality 1.1.5 board
* Klipper
* BLTouch
* Dual 5015 cooling
* Direct Drive conversion
* Brass nozzle

Material baseline:

* Sunlu PETG
* 240°C nozzle
* 75°C bed

Calibration baseline:

* BLTouch Z offset: 2.850
* Silicone spacers installed.
* SCREWS_TILT_CALCULATE mapped.
* Bed mesh validated.

## PETG Mesh Procedure

1. Install PETG textured PEI sheet.
2. Heat bed to **75°C**.
3. Wait **15 minutes**.
4. Home printer.
5. Run `SCREWS_TILT_CALCULATE` if hardware has been disturbed.
6. Run `BED_MESH_CALIBRATE`.
7. Save mesh as `PETG_75C`.

This file represents a known-good printer state.
