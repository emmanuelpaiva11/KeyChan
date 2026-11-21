# KeyChan

![KeyChan](images/hero.jpg)

A pocket-sized **color e-paper keychain** powered by ESP32-C3. Update images, status, QR codes, or text wirelessly via BLE — with deep sleep delivering **weeks of battery life** on a 50mAh LiPo.

## Features

- 🎨 **1.54" Color E-Paper** (400×400, SPI, partial refresh)
- 🔋 **50mAh LiPo + USB-C charging** (TP4056)
- 📱 **BLE GATT** — push images/text from phone/web
- 😴 **Deep Sleep** — ~18µA standby, weeks of runtime
- 🔧 **Custom PCB** — KiCad, 2-layer, compact
- 📦 **3D-Printed Case** — Snap-fit, keyring hole, button cutouts

## Hardware

| Component | Part | Qty |
|-----------|------|-----|
| Display | GoodDisplay 1.54" Color SPI (GDEY0154D67) | 1 |
| MCU | ESP32-C3 SuperMini | 1 |
| Battery | 50mAh 3.7V LiPo (202030) | 1 |
| Charging | TP4056 USB-C Module | 1 |
| Buttons | Tactile Switch SMD | 2 |
| PCB | Custom 2-layer (JLCPCB) | 1 |
| Case | PLA Filament | ~5g |

## Firmware Architecture

```
┌─────────────────────────────────────────────┐
│               FreeRTOS Tasks                │
├─────────────┬─────────────┬─────────────────┤
│  E-Paper    │    BLE      │     Sleep       │
│  Task       │    Task     │     Task        │
│  (SPI)      │  (GATT)     │  (Deep Sleep)   │
└──────┬──────┴──────┬───────┴────────┬────────┘
       │             │                │
       ▼             ▼                ▼
┌─────────────────────────────────────────────┐
│           Framebuffer + NVS                 │
│         (Images stored in flash)            │
└─────────────────────────────────────────────┘
```

- **E-Paper Task:** Partial/full refresh, double-buffering
- **BLE Task:** GATT server, image chunking (20-byte MTU)
- **Sleep Task:** Deep sleep, wake on button press / BLE activity
- **NVS Storage:** Persist last image across reboots

## Quick Start

### Hardware
1. Assemble PCB (JLCPCB gerbers in `hardware/kicad/gerbers/`)
2. Solder components (see BOM.csv for part numbers)
3. 3D print case (`hardware/cad/keychan.stl`)
4. Flash firmware (`idf.py flash`)

### Software
```bash
# Clone with submodules
git clone --recurse-submodules https://github.com/emmanuelpaiva11/KeyChan
cd KeyChan

# Build & flash (ESP-IDF)
. $HOME/esp/esp-idf/export.sh
idf.py build flash monitor
```

### BLE Usage
- Connect via any BLE scanner (nRF Connect, WebBLE)
- **Service UUID:** `0000ffe0-0000-1000-8000-00805f9b34fb`
- **Write Characteristic:** `0000ffe1-0000-1000-8000-00805f9b34fb`
- Send image data in chunks (see `docs/ble-protocol.md`)

## Documentation

| File | Description |
|------|-------------|
| `JOURNAL.md` | Daily build log (Forge requirement) |
| `BOM.csv` | Complete Bill of Materials with LCSC links |
| `docs/ble-protocol.md` | BLE GATT specification |
| `docs/build-guide.md` | Assembly instructions |
| `hardware/kicad/` | KiCad project files |
| `hardware/cad/keychan.f3d` | Fusion 360 source |
| `hardware/cad/keychan.step` | STEP file for manufacturing |

## Power

| Mode | Current | Est. Runtime (50mAh) |
|------|---------|----------------------|
| Deep Sleep | ~18µA | **~300 days** |
| BLE Advertising | ~500µA | ~100 days |
| E-Paper Refresh | ~25mA (2s) | 200+ refreshes |
| BLE Connected | ~2mA | ~1 day |

## License

MIT — see `LICENSE`.

---

*Built for [Hack Club Forge](https://forge.hackclub.com) — T3 Project*