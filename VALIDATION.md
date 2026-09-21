# 2026-09-14 — Ender 5 Pro Printer Baseline v1.2 Validation

**Printer Baseline v1.2 Status: Validated production baseline for OpenPivot PETG printing.**

## Summary

This validation completes the mechanical calibration of the Ender 5 Pro following installation of silicone bed spacers and manual validation of the existing front-mounted BLTouch probe geometry.

The resulting PETG bed mesh at **75°C** becomes the new production baseline for the OpenPivot project.

---

## Hardware Configuration

### Printer

* Printer: Creality Ender 5 Pro
* Mainboard: Creality v1.1.5
* Firmware: Klipper
* Bed Surface: Black textured PEI spring steel sheet (AliExpress)
* Bed Mounts: Silicone spacers (replacing stock springs)

### Probe

Existing BLTouch mounted on the front-centre toolhead (installed previously with the dual 5015 cooling shroud).

Validated offsets:

```ini
x_offset: 2.5
y_offset: -52
```

No changes to probe hardware were made during this validation.

---

## Probe Boundary Validation

The carriage was manually moved to each edge of the printable area to determine the maximum safe probe travel.

### Reachable Bed Screw Positions

| Screw       | Coordinates |
| ----------- | ----------- |
| Front Left  | X24.5 Y80   |
| Front Right | X194.5 Y80  |
| Rear Right  | X194.5 Y220 |
| Rear Left   | X24.5 Y220  |

Rear positions are the furthest reachable locations while remaining in front of the bed mounting screws.

### Bed Mesh Limits

Validated mesh boundaries:

```ini
mesh_min: 25,25
mesh_max: 170,165
probe_count: 9,9
```

**Validation notes**

* Probe correctly reaches the entire printable bed width.
* Right-hand side of the bed is now fully measurable.
* `Y=0` is **not** a valid probe location because the BLTouch body contacts the front aluminium extrusion before reaching the bed.
* Klipper correctly compensates for the `y_offset` during mesh generation, so `mesh_min: 25,25` remains a safe probing position.

---

## Screws Tilt Calibration

Bed tramming performed after a 15-minute heat soak at **75°C** using:

```ini
[screws_tilt_adjust]
screw1: 24.5,80
screw2: 194.5,80
screw3: 194.5,220
screw4: 24.5,220
```

### Final Accepted Result

| Corner      | Measured Z |
| ----------- | ---------: |
| Front Left  |     -0.164 |
| Front Right |     -0.153 |
| Rear Right  |     -0.148 |
| Rear Left   |     -0.161 |

Maximum deviation between corners:

**0.016 mm**

Result accepted as production baseline.

### Bed Spacer Compression Check

Measured gap between the underside of the heated bed and the Y-carriage after final tramming:

| Location | Gap |
|----------|----:|
| Front Left | 17.0 mm |
| Front Right | 17.3 mm |

The front-left silicone spacer is compressed approximately 0.3 mm more than the front-right spacer. This is consistent with the final tramming result and is considered normal spacer preload rather than a fault.


---

## Probe Accuracy Validation

`PROBE_ACCURACY SAMPLES=10` performed at multiple locations around the bed.

All coordinates listed in this section are nozzle coordinates used by Klipper. The BLTouch probe position is automatically offset by x_offset and y_offset during probing.

### Results

| Location                    | Range          | Standard Deviation |
| --------------------------- | -------------- | ------------------ |
| Near front-left             | 0.0138 mm      | 0.0039 mm          |
| Adjacent front-left point   | 0.0175 mm      | 0.0052 mm          |
| Additional corner positions | 0.011–0.036 mm | 0.003–0.011 mm     |

### Conclusion

Probe repeatability is excellent.

No evidence of inconsistent BLTouch triggering was observed. Probe repeatability remained within approximately ±0.01 mm at all tested locations. The remaining mesh variation is therefore dominated by bed flatness and mounting geometry rather than probe repeatability.

---

## PETG Bed Mesh Validation

### Test Conditions

* Bed temperature: **75°C**
* Heat soak: **15 minutes after the bed reached 75°C to allow the aluminium bed, PEI sheet, and silicone spacers to stabilise thermally.**
* Probe grid: **9×9**
* Algorithm: **bicubic**
* Bicubic tension: **0.2**

### Calibration Conditions

* Bed temperature: **75°C**
* Nozzle temperature: **150°C**
* Heat soak duration: **15 minutes**
* PEI sheet installed and cleaned prior to probing.

### Final Mesh Statistics

| Measurement  |        Value |
| ------------ | -----------: |
| Minimum      |    -0.253 mm |
| Maximum      |    -0.125 mm |
| Mesh Range   | **0.128 mm** |
| Mesh Average |    -0.180 mm |

### Saved Mesh Profile

```gcode
BED_MESH_PROFILE SAVE=PETG_75C
SAVE_CONFIG
```

The `PETG_75C` profile is the production mesh loaded for PETG prints at a 75°C bed temperature.

### Observations

* Previous front-left depression has been reduced substantially after correct tramming.
* Mesh now shows a gentle overall gradient rather than isolated peaks or valleys.
* Surface variation is smooth across the printable area with no abnormal discontinuities.

### Live Z Compensation

During the validation print (OP-TEST-001 Candidate v1.2), Klipper reported:

```text
Toolhead Z-Offset: 0.000 mm
```

No live BabyStep adjustment was applied during the print. The first layer was produced using only the saved BLTouch `z_offset` and the PETG_75C mesh profile.

### Result

**Mesh range of 0.128 mm is accepted as the PETG production baseline.**

---

## Comparison With Previous Baselines

| Configuration                              							| Bed Temp |   Mesh Range |
| ------------------------------------------ 							| -------: | -----------: |
| PLA with stock springs                     							|     65°C |     0.161 mm |
| PETG after silicone installation (initial) 							|     75°C |     ~0.37 mm |
| PETG after probe boundary calibration (before final tramming)         |     75°C |     0.276 mm |
| **PETG after final tramming**             							| **75°C** | **0.128 mm** |

This represents approximately a **65% reduction** in PETG mesh variation compared to the initial silicone spacer calibration.

---

## Configuration Validated

The following configuration is validated for Baseline v1.2:

```ini
[bltouch]
x_offset: -2.5
y_offset: -52

[screws_tilt_adjust]
screw1: 24.5,80
screw2: 194.5,80
screw3: 194.5,220
screw4: 24.5,220

[bed_mesh]
speed: 120
horizontal_move_z: 5
mesh_min: 25,25
mesh_max: 170,165
probe_count: 9,9
algorithm: bicubic
bicubic_tension: 0.2
fade_start: 1
fade_end: 10
```

---

## Validation Status

| Test                            | Status     |
| ------------------------------- | ---------- |
| Probe boundary mapping          | ✅ Passed   |
| Bed screw coordinate validation | ✅ Passed   |
| Silicone spacer tramming        | ✅ Passed   |
| BLTouch repeatability           | ✅ Passed   |
| PETG 75°C bed mesh              | ✅ Passed   |
| Baseline v1.2 configuration     | ✅ Accepted |

---

## Notes

This validation supersedes the previous PETG bed mesh baseline and becomes the reference configuration for future OpenPivot printer testing and production prints.

No further mechanical bed adjustments are recommended unless:

* the bed surface is replaced,
* silicone spacers are removed,
* the probe mount geometry changes,
* or the hotend/toolhead assembly is modified.

### Final Validation Conclusion

Baseline v1.2 establishes the current mechanical reference configuration for the Ender 5 Pro. Future printer modifications (toolhead geometry, bed hardware, probe mount, hotend, or bed surface replacement) should be validated against this baseline before updating production printer configuration.
Silicone spacers were initially installed with uneven preload. After SCREWS_TILT_CALCULATE the front-left spacer required additional relaxation, resulting in visibly more even compression across all four spacers.
