## Current Production Baseline

**Baseline:** v1.3 (26 September 2026)

The current production baseline validates the migration from the Creality v1.1.5 electronics to the BTT SKR Mini E3 V3.0 while retaining the existing OpenPivot mechanical configuration.

Validated production configuration includes:

- SKR Mini E3 V3.0 running Klipper.
- TMC2209 UART drivers.
- SpreadCycle motion configuration for X and Y axes.
- PETG hardened steel nozzle production profile (240°C / 75°C).
- PETG_75C production bed mesh used for both PETG and PLA-F.

## 2026-09-21 — Baseline v1.3

- Enabled OrcaSlicer Arc Fitting in printer profile.
- Added Klipper `[gcode_arcs]` support (`resolution: 0.1`).
- Added `[exclude_object]` support for Orca object cancellation.
- Verified G3 arc commands generated in sliced G-code.

## v0.1.2-alpha — Mechanical Baseline

### Added

* Silicone spacer validation.
* Calibration map documentation.
* Measured SCREWS_TILT_CALCULATE coordinates.

### Changed

- BLTouch probe calibration finalised (`z_offset = 2.850 mm`).
- Probe geometry confirmed (`x_offset = -2.5 mm`, `y_offset = -52.0 mm`).
- Bed tramming workflow standardised around `SCREWS_TILT_CALCULATE`.

### Validated

* BLTouch repeatability across the bed.
* 9×9 PETG mesh generation after heat soak.

### Fixed

* Bed tramming imbalance caused by uneven silicone spacer preload after installation.

### 2026-09-21 – OrcaSlicer compatibility

Added Klipper modules:

- `[exclude_object]`
- `[gcode_arcs]` with `resolution: 0.1`

Reason: Enables OrcaSlicer object cancellation and G2/G3 arc support.