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
x_offset: -2.5
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


## 2026-09-26 — SKR Mini E3 V3.0 Motion System Validation (Baseline v1.3)

**Printer Baseline v1.3 Status: Motion system validated following migration to the BTT SKR Mini E3 V3.0.**

## Summary

This validation documents the migration of the Ender 5 Pro from the Creality v1.1.5 mainboard to the **BigTreeTech SKR Mini E3 V3.0** running Klipper with integrated TMC2209 UART drivers.

The existing OpenPivot mechanical configuration was retained, including the direct-drive conversion, silicone bed spacers, dual 5015 cooling shroud, and front-mounted BLTouch. Validation focused on confirming correct driver configuration, probe calibration, and motion reliability following the electronics upgrade.

This validation becomes the reference baseline for **Baseline v1.3**.

---

## Hardware Configuration

### Printer

| Component       | Previous             | Current                     |
| --------------- | -------------------- | --------------------------- |
| Printer         | Creality Ender 5 Pro | Creality Ender 5 Pro        |
| Mainboard       | Creality v1.1.5      | **BTT SKR Mini E3 V3.0**    |
| MCU             | ATmega1284P          | STM32G0B1                   |
| Firmware        | Klipper              | Klipper                     |
| Stepper Drivers | Creality TMC2208     | Integrated **TMC2209 UART** |

### Mechanical Configuration

The following hardware remained unchanged from Baseline v1.2:

* Direct-drive extruder conversion using the 17HS3401S motor and dual-gear extruder.
* Dual 5015 part cooling shroud.
* Silicone bed spacers.
* Textured PEI spring steel build surface.
* Front-mounted BLTouch probe.

No mechanical modifications were made during this validation.

---

## BLTouch Validation

The BLTouch installation was revalidated after migration to the SKR Mini.

### Validated Probe Configuration

```ini
[bltouch]
x_offset: -2.5
y_offset: -52
```

### Safe Z Home

```ini
[safe_z_home]
home_xy_position: 112.5,162
speed: 50
z_hop: 10
```

The probe homes safely within the printable area and correctly clears the front extrusion.

### Final Production Z Offset

```ini
z_offset = 2.825
```

### Validation Result

* First layer completed with **no BabyStep adjustment**.
* PLA-F validation prints completed successfully using only the saved BLTouch offset and production bed mesh.

**Result accepted as the production Z offset for Baseline v1.3.**

---

## Bed Mesh Validation

The previously validated PETG production mesh from Baseline v1.2 was retained.

### Production Mesh Configuration

```ini
mesh_min: 25,25
mesh_max: 170,165
probe_count: 9,9
algorithm: bicubic
bicubic_tension: 0.2
fade_start: 1
fade_end: 10
```

### Mesh Usage

| Material | Mesh Used  |
| -------- | ---------- |
| PETG     | `PETG_75C` |
| PLA-F    | `PETG_75C` |

A separate PLA mesh was intentionally removed.

The PETG_75C mesh is now the single production bed mesh for both PETG and PLA-F. Validation printing confirmed excellent first-layer quality for both materials using the same mesh profile, eliminating the need to maintain separate PLA and PETG meshes.

---

## Stepper Driver Validation

### Initial Symptoms

The first prints after installation of the SKR Mini showed intermittent layer shifts:

| Print                      | Result              |
| -------------------------- | ------------------- |
| PETG spacer batch          | Random X-axis shift |
| PETG temperature tower     | Single Y-axis shift |
| Klipper motion tests       | Passed              |
| MCU / communication errors | None                |

Layer shifts occurred in different directions between prints, suggesting missed motor steps rather than a repeatable mechanical alignment fault.

### Mechanical Verification

The following checks were completed before altering driver configuration:

| Test                             | Result   |
| -------------------------------- | -------- |
| X belt tension                   | ✅ Passed |
| Y belt tension                   | ✅ Passed |
| X pulley security                | ✅ Passed |
| Manual XY movement               | ✅ Smooth |
| High-speed Klipper movement test | ✅ Passed |
| Homing repeatability             | ✅ Passed |

No mechanical cause for the shifts was identified.

---

## Driver Configuration Changes

### Final Production Configuration

```ini
[tmc2209 stepper_x]
run_current: 0.80
stealthchop_threshold: 0

[tmc2209 stepper_y]
run_current: 0.80
stealthchop_threshold: 0

[tmc2209 stepper_z]
run_current: 0.65
stealthchop_threshold: 999999
```

### Configuration Changes

| Setting       | Previous |   Production |
| ------------- | -------: | -----------: |
| X run current |   0.70 A |   **0.80 A** |
| Y run current |   0.70 A |   **0.80 A** |
| X StealthChop |  Enabled | **Disabled** |
| Y StealthChop |  Enabled | **Disabled** |
| Z StealthChop |  Enabled |      Enabled |

X and Y now operate in SpreadCycle while moving, while Z remains in StealthChop for quiet vertical movement.

---

## TMC2209 Driver Validation

`DUMP_TMC` was used throughout validation to confirm driver operation.

### UART Validation

Validation confirmed:

* UART communication functioning correctly.
* `IFCNT` increased normally after configuration writes.
* No driver faults (`GSTAT = 0`) after startup.

### SpreadCycle Validation

`DUMP_TMC` was executed during an active print.

#### Results

| Axis | Result                               |
| ---- | ------------------------------------ |
| X    | Sampled while stationary (`stst=1`). |
| Y    | Sampled while moving (`TSTEP=188`).  |

During motion, the Y-axis driver no longer reported the `stealth=1` flag, confirming that the driver had switched into **SpreadCycle** exactly as configured.

### Validation Conclusion

The SKR Mini correctly applies the X/Y driver configuration.

No startup macros, manual register writes, or UART workarounds are required.

---

## PETG Temperature Tower Validation (Steel Nozzle)

### Test Conditions

| Setting           |                              Value |
| ----------------- | ---------------------------------: |
| Material          |                         Sunlu PETG |
| Nozzle            |                     Hardened Steel |
| Bed               |                               75°C |
| Fan Profile       | Updated v1.3 cooling profile		 |
| Temperature Range |                      250°C → 230°C |
| Temperature Step  |                                5°C |

### Cooling Profile Validation

| Cooling Setting  |    Value |
| ---------------- | -------: |
| No cooling first | 7 layers |
| Full fan at      |  Layer 7 |
| Minimum fan      |      15% |
| Maximum fan      |  **40%** |

This profile replaces the previous 35% PETG cooling profile. Increasing maximum cooling to 40% after layer 7 improved PETG consistency with the hardened steel nozzle while avoiding the corner curling observed at higher fan speeds.

### Results

| Temperature | Observation                                            |
| ----------- | ------------------------------------------------------ |
| 250°C       | Increased stringing.                                   |
| 245°C       | Good layer adhesion with minor stringing.              |
| **240°C**   | Best overall balance of surface quality and stringing. |
| 235°C       | Slight reduction in gloss with acceptable quality.     |
| 230°C       | Beginning of under-extrusion and weaker bridging.      |

### Production Temperature

**240°C** becomes the production PETG nozzle temperature when using the hardened steel nozzle.

---

## PLA-F Motion Validation

Following the X/Y TMC2209 driver configuration changes, a PLA-F validation print of the OpenPivot spacer test pieces was completed using the production slicer profile.

### Test Conditions

| Setting         |      Value |
| --------------- | ---------: |
| Material        |      PLA-F |
| Bed Temperature |       65°C |
| Bed Mesh        | `PETG_75C` |
| Z Offset        |   2.825 mm |

### Results

| Test                    | Status          |
| ----------------------- | --------------- |
| First layer             | ✅ Excellent     |
| Layer shifts            | ✅ None observed |
| Hole geometry           | ✅ Accurate      |
| Top surface finish      | ✅ Fully closed  |
| Dimensional consistency | ✅ Passed        |

The printed spacers showed no evidence of X or Y layer shifts and required only minimal removal of cosmetic stringing.

### Validation Conclusion

PLA-F motion testing passed using the new SKR Mini driver configuration without any firmware or slicer changes beyond the validated production profile.

---

## Production Configuration (Baseline v1.3)

### Motion Configuration

| Parameter                    | Production Value |
| ---------------------------- | ---------------: |
| Max Velocity                 |         300 mm/s |
| Max Acceleration             |       3000 mm/s² |
| Pressure Advance             |            0.036 |
| Pressure Advance Smooth Time |          0.040 s |

### Driver Configuration

| Axis     | Run Current | Driver Mode              |
| -------- | ----------: | ------------------------ |
| X        |  **0.80 A** | SpreadCycle while moving |
| Y        |  **0.80 A** | SpreadCycle while moving |
| Z        |      0.65 A | StealthChop              |
| Extruder |      0.80 A | UART controlled          |

### Cooling Configuration

| Setting          |    Value |
| ---------------- | -------: |
| No cooling first | 7 layers |
| Full fan at      |  Layer 7 |
| Minimum fan      |      15% |
| Maximum fan      |      40% |

---

## Validation Status

| Test                                | Status     |
| ----------------------------------- | ---------- |
| SKR Mini installation               | ✅ Passed   |
| BLTouch offset validation           | ✅ Passed   |
| Safe Z Home validation              | ✅ Passed   |
| Z Offset validation                 | ✅ Passed   |
| PETG production mesh reuse          | ✅ Passed   |
| UART communication validation       | ✅ Passed   |
| SpreadCycle transition validation   | ✅ Passed   |
| PLA-F motion validation             | ✅ Passed   |
| PETG steel nozzle temperature tower | ✅ Passed   |
| Baseline v1.3	configuration	      | ✅ Accepted |

---

## Notes

Baseline v1.3 supersedes Baseline v1.2 for the printer electronics and motion configuration while retaining the validated mechanical geometry established in Baseline v1.2.

The PETG production mesh remains unchanged from Baseline v1.2 and is now used as the single production mesh for both PETG and PLA-F.

The initial layer-shift investigation identified intermittent skipped steps immediately after installation of the SKR Mini E3 V3.0. Mechanical causes were eliminated through inspection and motion testing, and the production configuration was updated to increase X/Y motor current to **0.80 A** while operating the X and Y axes in SpreadCycle during motion.

PLA-F validation completed successfully with no observed layer shifts.

The SKR Mini motion configuration is accepted as the production electronics baseline for subsequent OpenPivot PETG production prints.

### Final Validation Conclusion

Baseline v1.3 establishes the production electronics and motion reference configuration for the Ender 5 Pro using the BTT SKR Mini E3 V3.0.

Future validation is only required if:

* the toolhead mass changes,
* the stepper motors are replaced,
* the TMC driver configuration is modified,
* input shaper is recalibrated,
* or the motion system hardware is altered.

### Additional Validation – BLTouch Spacer Production Batch

A production batch of six BLTouch spacers was printed using the Baseline v1.3 PLA-F profile after updating the SKR Mini E3 V3.0 driver configuration.

**Print Conditions**

* Material: PLA-F
* Nozzle: 225°C
* Bed: 65°C
* Bed Mesh: PETG_75C
* X/Y Run Current: 0.80 A
* X/Y Driver Mode: SpreadCycle during motion

**Results**

| Observation          | Result                                                                                          |
| -------------------- | ----------------------------------------------------------------------------------------------- |
| Completed parts      | 5 of 6                                                                                          |
| Layer shifts         | None observed                                                                                   |
| Dimensional accuracy | Passed                                                                                          |
| Surface quality      | Improved over previous 230°C profile                                                            |
| Failure mode         | One part detached from the PEI surface during printing. Remaining parts completed successfully. |

**Conclusion**

The completed spacers confirmed that the Baseline v1.4-alpha motion configuration eliminated the previously observed intermittent X/Y layer shifts. The single failed part was consistent with a local bed adhesion failure on a very small component rather than a motion-system error.

### Motor Thermal Validation

Motor temperatures were checked immediately after completing the PLA-F BLTouch spacer validation print using the Baseline v1.3 production configuration.

| Axis | Observation                               |
| ---- | ----------------------------------------- |
| X    | Warm to the touch after print completion. |
| Y    | Cold to the touch after print completion. |

The X-axis motor temperature is consistent with the increased mass of the direct-drive toolhead operating at `run_current: 0.80`. No overheating, skipped steps, or thermal shutdown behaviour was observed.

**Result:** Motor temperatures accepted as part of the Baseline v1.3 production validation.

