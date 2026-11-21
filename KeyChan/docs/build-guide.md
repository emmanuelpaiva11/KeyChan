# KeyChan Build Guide

## Parts Required
See BOM.csv for complete list with LCSC links.

## PCB Assembly

1. **Solder passives first** (0603 resistors/capacitors)
2. **Solder MCU** (ESP32-C3 SuperMini) — align carefully, hot air or fine tip
3. **Solder TP4056 module** — check USB-C orientation
4. **Solder tactile switches** — SMD, 6x6mm
5. **Solder FPC connector** — for e-paper display
6. **Install LiPo** — JST-PH 2-pin or solder directly
7. **Install heatset inserts** in 3D printed case (M3x5)
8. **Assemble case** — snap fit, install M3x8 screws

## Flashing Firmware

```bash
# Install ESP-IDF v5.3+
git clone --recursive https://github.com/espressif/esp-idf.git ~/esp/esp-idf
cd ~/esp/esp-idf
./install.sh esp32c3
. ~/esp/esp-idf/export.sh

# Build & flash
idf.py set-target esp32c3
idf.py build flash monitor
```

## BLE Testing

1. Open nRF Connect app
2. Scan for "KeyChan"
3. Connect → enable notifications on write characteristic
4. Send image data per `docs/ble-protocol.md`

## Case Assembly

1. Print `hardware/cad/keychan.stl` (PLA, 0.2mm, 3 perimeters, 15% infill)
2. Install 4× M3x5 heatset inserts (heat with soldering iron)
3. Place PCB in bottom half
4. Snap top half on
5. Install 4× M3x8 screws