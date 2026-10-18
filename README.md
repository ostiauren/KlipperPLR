# PLR Klipper

KlipperPLR(forked from [YUMI_PLR](https://github.com/Yumi-Lab/YUMI_PLR/)) is a simple print recovery system for Klipper, a 3D printer firmware. It allows you to resume prints after a power loss or other types of MCU disconnection interruption. Please note there is no guarantee that it will work in 100% of cases because the Z-axis must not have moved, so do not touch the machine in case of a power cut.

## Prerequisites
having already installed Klipper, Moonraker, and Mainsail (you can use Kiauh).

To install KlipperPLR Klipper, follow the steps below:

## Installation
* Clone the KlipperPLR Klipper repository from GitHub to your local machine:
    ```bash
    git clone -b custom https://github.com/ostiauren/KlipperPLR.git
    cd KlipperPLR
    ./install.sh
    ```

* start-gcode add in your slicer:
    ```bash
    G31
    save_last_file
    SAVE_VARIABLE VARIABLE=was_interrupted VALUE=True
    ```

* end-gcode add in your slicer (or at the end of your `PRINT_END` macro, so it works for every slicer):
    ```bash
    SAVE_VARIABLE VARIABLE=was_interrupted VALUE=False
    clear_last_file
    G31
    ```
    If `was_interrupted` is never reset, a finished print still looks like an interrupted one.
* Before layer change G-gcode add in your slicer:
    ```bash
    LOG_Z
    ```
* To resume printing after a power cut, simply execute the 'RESUME_INTERRUPTED' macro in the MAINSAIL console or via the Macro button on the MAINSAIL dashboard.

## Differences from upstream (this `custom` branch)
Forked from [bigtreetech/KlipperPLR](https://github.com/bigtreetech/KlipperPLR) and kept on the `custom` branch.
* **Z matching fixed.** The slicer can write the same height in different ways (`Z.8`, `Z0.8`, `Z2` instead of `Z2.0`).
  `RESUME_INTERRUPTED` now formats the saved Z without trailing zeros and `plr.sh` accepts it with or without a leading zero,
  matching the dot literally. Before, a layer below 1 mm (or a whole-number height) never matched and the "resume" file
  contained the entire print.
* **Refuses instead of restarting the print.** If the saved Z is not found in the gcode, `plr.sh` deletes the resume file and
  exits with an error, so `SDCARD_PRINT_FILE` fails instead of printing the whole file again.
* **Scripts install to `~/printer_data/config/plr`** instead of `~/printer_data/plr`, so config backups (for example Klipper-Backup) include them.
* **Does not overwrite `gcode_shell_command.py`** when Klipper already has one (Kalico ships its own).
* **Moonraker update entry** follows this fork's `custom` branch.

## Known Bugs:
The preview image of the gcode file is not rebuilt.
 




