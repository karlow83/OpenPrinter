# OpenPrinter Ender 5 Pro Baseline

## Current Production Baseline

**Baseline:** v1.3 (2026-09-26)

This baseline validates the migration from the Creality v1.1.5 mainboard to the BTT SKR Mini E3 V3.0 while retaining the existing OpenPivot mechanical configuration.

Validated components include:

- SKR Mini E3 V3.0 installation.
- TMC2209 UART driver configuration.
- SpreadCycle motion configuration on X/Y.
- PETG hardened steel production profile.
- PETG_75C production bed mesh for both PETG and PLA-F.

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
