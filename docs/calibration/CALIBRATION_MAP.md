# Ender 5 Pro Calibration Map

## BLTouch Geometry

Probe mounted **2.5 mm left** and **52 mm in front** of the nozzle.

| Offset |    Value |
| ------ | -------: |
| X      |  -2.5 mm |
| Y      | -52.0 mm |
| Z      | 2.850 mm |

## Bed Screw Map

Coordinates below are **nozzle coordinates** used by `SCREWS_TILT_CALCULATE`.

| Screw       |     X |   Y |
| ----------- | ----: | --: |
| Front Left  |  24.5 |  80 |
| Front Right | 194.5 |  80 |
| Rear Right  | 194.5 | 220 |
| Rear Left   |  24.5 | 220 |

## Validated Probe Limits

Physical probe offsets:

```ini
x_offset: 2.5
y_offset: -52
```

Validated mesh area:

```ini
mesh_min: 25,25
mesh_max: 170,165
```

Reason:

The BLTouch cannot safely probe Y=0 because the probe housing contacts the front aluminium extrusion before reaching the bed surface.

## Calibration Workflow

1. Heat bed to operating temperature.
2. Heat soak for 20 minutes.
3. Home printer.
4. Run `SCREWS_TILT_CALCULATE`.
5. Adjust silicone spacers.
6. Repeat until all screws are within tolerance.
7. Run `BED_MESH_CALIBRATE`.
8. Save mesh profile.
9. Do **not** run `PROBE_CALIBRATE` unless probe or nozzle height changes.
