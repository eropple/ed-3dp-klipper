# Ed's Klipper Configs #

_The SV06, SV06 Plus, and Neptune 3 Plus configs are all based on configs graciously shared by [bassamanator](https://github.com/bassamanator/Sovol-SV06-firmware).

Check the individual branches in this repository for details.

This branch contains the Klipper configuration and firmware for the **Sovol SV06** 3D printer.

## Outline

- [Features](#features)
- [Stay Up-to-Date](#stay-up-to-date)
- [Preface](#preface)
- [Installation Steps](#installation-steps)
  - [Before You Begin](#before-you-begin)
  - [Flash Firmware](#flash-firmware)
  - [Download Klipper Configuration](#download-klipper-configuration)
- [Initial Steps](#initial-steps)
  1. [Adjust Configuration with MCU Path](#adjust-configuration-with-mcu-path)
  2. [Configure Your Printer](#configure-your-printer)
- [Adjust Your Slicer](#adjust-your-slicer)
- [Directory Structure](#directory-structure)
- [Support Me](#support-me)
- [FAQ](#faq)
- [Useful Resources](#useful-resources)
- [Sovol Official Links](#sovol-official-links)
- [Sources](#sources)

## Features

- 💥 This Klipper configuration is an _endpoint_, meaning that it contains **everything** that you could possibly need in order to have an excellent Klipper experience! 💥
- <img src="./images/party_blob.gif" width="20" alt=''/> Filament runout sensor usage implemented. <img src="./images/party_blob.gif" width="20" alt=''/>
- Minimum configuration settings for Mainsail to work.
- Bed model and texture to use in SuperSlicer/PrusaSlicer.
- Macros
  - **Improved** mechanical gantry calibration/`G34` macro that provides the user audio feedback, and time to check the calibration.
  - Misc macros: `PRINT_START`, `CANCEL_PRINT`, `PRINT_END`, `PAUSE`, `RESUME`.
  - Parking macros (parks the printhead at various locations): `PARKFRONT`, `PARKFRONTLOW`, `PARKREAR`, `PARKCENTER`, `PARKBED`.
  - Calibration macros: `PID_TEST_BED`, `PID_TEST_HOTEND`, `PID_TEST_ALL`, `DO_PROBE_CALIBRATE`, `DO_BED_CALIBRATE`.
  - Load/unload filament macros.
  - Purge line macro.

## Installation Steps

This document assumes you know how to install Klipper. I use KIAUH. You don't have to.

A precompiled firmware binary is available in `bin/`. You can flash it in the standard SV06 manner.

## Initial Steps

### Adjust Configuration with MCU Path

I've marked values you need to change with a `CHANGE THIS` in the configs.

### Configure Your Printer

💡 I recommend no filament be loaded for any of these steps.

💡 Find explanations for gcode commands at [https://marlinfw.org/](https://marlinfw.org/) and [https://www.klipper3d.org/](https://www.klipper3d.org/G-Codes.html).

You will be pasting/typing these commands into the Mainsail console.

1. `G28`
   1. Check to see if `X` and `Y` max positions (`G90`, `G1 X223 F3000`, `G1 Y223 F3000`) can be reached, and adjust `position_max`, if necessary. You can probably go all the way up to `225` for `X` and `Y` both, however, I would not recommend it.
2. Do a `G34`; mechanical gantry calibration. After the controlled collision against the beam at the top, there will be a 10 second pause for you to verify that both sides of the gantry are pressed up against the `stoppers` at the top. You will hear a succession of beeps.
   1. Figure out your `Z` `position_max` by baby stepping your way up to the beam. The range is 250 to 261 from what I've seen, could be even higher for you. Adjust `position_max`, if necessary. I can go all the way to 258, however, I would not print anything higher than 257.
3. PID tune the bed, but first move the printhead to the center. Ideally, all PID tuning should occur at the temperatures that you print most at.
   1. `PID_TEST_BED`
   2. `SAVE_CONFIG` (once completed)
4. PID tune the extruder while part cooling fan runs at 25%.
   1. `PID_TEST_HOTEND`
   2. `SAVE_CONFIG` (once completed)
5. Adjust `z_offset`. Make sure your nozzle is very clean. Do the [Paper test](https://www.klipper3d.org/Bed_Level.html?h=probe_calibrate#the-paper-test).
   1. `DO_PROBE_CALIBRATE`
   2. Follow z-offset setup in Mainsail.
   3. `SAVE_CONFIG` (once completed)
6. Create a bed mesh.
   1. `DO_BED_CALIBRATE`
   2. `SAVE_CONFIG` (once completed)

If you've made it here, then your printer has been Klipperized, and is ready to print!

But first, adjust your slicer.

## Adjust Your Slicer

You need to adjust the start and end gcode in your slicer. The relevant macros are `PRINT_START` and `PRINT_END`. Find instructions [here](https://ellis3dp.com/Print-Tuning-Guide/articles/passing_slicer_variables.html#slicer-start-g-code).

If you would like to print a purge line before your print starts, at the end of your start gcode, on a new line add `PURGE_LINE`. Here's an example:

```
PRINT_START BED=[first_layer_bed_temperature] HOTEND={first_layer_temperature[initial_extruder]+extruder_temperature_offset[initial_extruder]} CHAMBER=[chamber_temperature]
PURGE_LINE
```

## Directory Structure

This repository contains many files and folders. Some are _necessary_ for this Klipper configuration to work, others are not.

- **Necessary** items are marked with a ✅.
- Items that can _optionally_ be deleted are marked with a ❌.

```
├── cfgs ✅
│   ├── adxl-direct.cfg
│   ├── adxl-rp2040.cfg
│   ├── adxl-rpi-pico-2x.cfg
│   ├── MECHANICAL_GANTRY_CALIBRATION.cfg
│   ├── misc-macros.cfg
│   ├── PARKING.cfg
│   └── TEST_SPEED.cfg
├── CODE_OF_CONDUCT.md ❌
├── CONTRIBUTING.md ❌
├── .github ❌
│   ├── FUNDING.yml
│   └── ISSUE_TEMPLATE
│       ├── bug_report.md
│       └── feature_request.md
├── .gitignore ❌
├── images ❌
│   ├── cup-border.png
│   ├── githubstar.gif
│   ├── heart.gif
│   ├── logo_white_stroke.png
│   └── party_blob.gif
├── misc ❌
│   ├── klipper-v0.11.0-148-g52f4e20c.bin
│   ├── M503-output.yml
│   ├── marlin-SV06V2.0.0A_2.24.bin
│   ├── SuperSlicer_config_bundle.ini
│   ├── sv06-buildPlate.png
│   ├── SV06-buildPlate.stl
│   └── SV06-texture.svg
├── moonraker.conf ✅
├── printer.cfg ✅
├── README.md ❌
└── .vscode ❌
    └── settings.json
```

## Support Me

Please ⭐ star ⭐ this repository!

If you found my work useful, please consider buying me a [<img src="./images/logo_white_stroke.png" height="20" alt='Ko-fi'/>](https://ko-fi.com/bassamanator).

## FAQ

##### How do I import a SuperSlicer configuration bundle (`SuperSlicer_config_bundle.ini`) into SuperSlicer?

Please see [this discussion](https://github.com/bassamanator/Sovol-SV06-firmware/discussions/13).

##### How do I print using SuperSlicer?

Please see [this discussion](https://github.com/bassamanator/Sovol-SV06-firmware/discussions/14).

##### When does beeping occur?

The printer will beep upon:

- Filament runout.
- Filament change/`M600`.
- Upon `PRINT_END`.
- `MECHANICAL_GANTRY_CALIBRATION`/`G34`.

##### How do I disable beeping?

Make the following changes according to your needs. All beeping will be disabled _except_ during gantry calibration.

| File            | `cfgs/misc-macros.cfg`     |
| --------------- | -------------------------- |
| Section         | `[gcode_macro _globals]`   |
| Variable        | `variable_beeping_enabled` |
| Disable beeping | `0`                        |
| Enable beeping  | `1`                        |

##### I want to use a filament sensor. How do I set it up?

You can find information about the physical setup [here](https://github.com/bassamanator/everything-sovol-sv06#filament-sensor).

##### I have a simple filament sensor connected. How do I enable/disable it?

Make the following changes according to your needs.

| File           | `cfgs/misc-macros.cfg`             |
| -------------- | ---------------------------------- |
| Section        | `[gcode_macro _globals]`           |
| Variable       | `variable_filament_sensor_enabled` |
| Disable sensor | `0`                                |
| Enable sensor  | `1`                                |

##### My filament runout sensor works, but I just started a print without any filament loaded. What gives?

A simple runout sensor can only detect a change in state. So, if you start a print without filament loaded, the printer will not know that there is no filament loaded. You should test your sensor by having filament loaded, starting a print, then cutting the filament. The expected behaviour is that the print will pause, and as long as you have beeping enabled, you will hear 3 annoying beeps.

##### What happens when I put in `M600`/colour change at a certain layer?

1. The printer will beep 3 times (not annoyingly).
2. Printing will stop.
3. The printhead will park itself front center.
4. The hotend will turn off, but the bed will remain hot.

##### What happens when I pause a print?

Same behaviour as `M600`/colour change _except_ there won't be any beeping.

##### What happens when filament runs out?

_If_ you have a working filament sensor, the same behaviour as `M600`/colour change will occur _except_ the beeps will be fairly annoying.

##### How do I resume a print after a colour change or filament runout?

⚠️ _Do not disable the stepper motors during this process!_

The printhead is now parked front center waiting for you to insert filament. You will:

1. Heat up the hotend to the desired temperature.
   - Use your Klipper dashboard.
2. Purge (push) some filament through the nozzle.
   - Use your Klipper dashboard, and extrude maybe 50mm (for a colour change you probably want to extrude more).
   - OR, you can push some filament by hand _making sure to first disengage the extruder's spring loaded arm_.
3. Hit resume in your Klipper dashboard.

## Useful Resources

- [Everything Sovol SV06](https://github.com/bassamanator/everything-sovol-sv06)
- [RP2040-Zero ADXL345 Connection Klipper](https://github.com/bassamanator/rp2040-zero-adxl345-klipper)
- ⭐⭐⭐⭐⭐ [Ellis' Print Tuning Guide](https://ellis3dp.com/Print-Tuning-Guide)
- [Simplify3D Print Quality Troubleshooting Guide](https://www.simplify3d.com/resources/print-quality-troubleshooting/)

## Sovol Official Links

- [SV06 Marlin Source Code](https://github.com/Sovol3d/Sv06-Source-Code)
- [SV06 Models](https://github.com/Sovol3d/SV06-Fully-Open-Source)
- [SV06 Plus Marlin Source Code and Models](https://github.com/Sovol3d/SV06-PLUS)

## Sources

- https://www.klipper3d.org
- https://ellis3dp.com/Print-Tuning-Guide
- https://github.com/strayr/strayr-k-macros
- https://docs.vorondesign.com/build/software/miniE3_v20_klipper.html
- https://github.com/spinixguy/Sovol-SV06-firmware
- https://www.printables.com/model/378915-sovol-sv06-buildplate-texture-and-model-for-prusas
- https://github.com/AndrewEllis93/Ellis-SuperSlicer-Profiles

[![ko-fi](https://ko-fi.com/img/githubbutton_sm.svg)](https://ko-fi.com/H2H0HIHTH)
