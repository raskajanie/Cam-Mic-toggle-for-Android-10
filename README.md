# Sensor Tiles

Magisk module for **Android 10**. Quick Settings tiles that keep the microphone and the camera blocked until you tap them again. A watchdog puts the block back on new installs and whenever AppOps is reset.

## What it does

Two tiles: "Microphone access" and "Camera access". A tap toggles the state. While a tile is blocking:

1. **AppOps `ignore` (uid-mode)** for every app with uid ≥ 10000 — normal apps, updated system apps, Google apps. `cmd appops set --uid` sets one mode for the whole uid. Disabled apps are picked up when they are enabled again.
2. The previous mode of each uid is saved. Unblock restores that mode, it does not force `allow`.
3. **Watchdog** (every 5 seconds, full recheck once a minute) blocks new installs and restores `ignore` if a permission grant cleared AppOps.

The tile runs `su -c` and reads exactly `blocked` or `allowed`. The module grants root in Magisk (`allow`, no log, no toast, no expiry) via `/data/adb/magisk/magisk`. If the APK was not mounted into `/system/app`, it is installed with `pm install` after boot. The module title in the Magisk app follows the system language: ru, en, de, es, it, fr, zh, ja, pt.

Removing the module lifts the block after boot and uninstalls the tile app.

Processes with uid < 10000 (`system`, `audioserver`, and similar) are not covered. This is not a kernel mute.

## Install

1. Flash `SensorTiles-Magisk.zip` in Magisk and reboot.
2. Quick Settings → edit → add "Microphone access" and "Camera access".
3. If another mic/camera module is already installed, the installer warns you. Keep one of them, or they will overwrite each other's AppOps.

Unblock the tiles before disabling the module. Otherwise the watchdog stops and `ignore` stays until the module is removed. Removal restores the saved modes.

## Files

| File | Role |
|---|---|
| `system/app/SensorTiles/SensorTiles.apk` | Tiles, package `com.sensortiles`, targetSdk 29 |
| `sensors.sh` | `mic` / `cam`: `block`, `unblock`, `toggle`, `status`, `info`, `daemon` |
| `service.sh` | After boot: language, APK install, root grant, re-apply block, watchdog |
| `lang.sh` | Module strings |
| `uninstall.sh` | Deferred unblock and `pm uninstall` |

State: `/data/adb/sensor_tiles/` (`mic.blocked`, `cam.blocked`, saved modes, `log.txt`).

Author: raskajanie
