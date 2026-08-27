# Sentinel

Sentinel is an automated electronic climbing belay device ("Smart Belay" /
"Electric GriGri"): a mechatronic system built on an ESP32 (C++ / PlatformIO)
that reads a rotary encoder, load cell, and IMU in real time to control a
servo/BLDC motor mechanism, managing rope slack and triggering a fast-acting
emergency brake on falls.

This is a Master's Thesis project in Industrial Engineering at NTNU
Trondheim, supervised by Amund Skavhaug.

## Repository structure

- `src/`, `include/`, `lib/`, `test/`, `platformio.ini` — ESP32 firmware,
  built with [PlatformIO](https://platformio.org/).
- `docs/` — technical specs, pinouts, hardware datasheets, and project
  documentation.
  - `docs/DEV_LOG.md` — running history of project decisions and changes.
  - `docs/thesis/` — the academic Master's Thesis documents (LaTeX).
- `CAD/` — mechanical CAD files (not tracked in version control; kept
  local-only).

## Firmware

The firmware targets an ESP32 board using the Arduino framework via
PlatformIO. To build and upload:

```bash
pio run --target upload
```

## Status

Early development stage — see `docs/DEV_LOG.md` for the current state and
next steps.
