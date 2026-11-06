# Ender-3 S1 F1 diagnostics — R2

## Fixed

- This board-specific build now reads and writes the January 2026 firmware's
  settings identifier. It still checks the EEPROM version, record size and CRC.
  A failed check is reported on serial without automatically writing defaults.
- Added startup messages after serial comes up, before reading settings, and
  after the settings routine returns. The last message alone does not verify
  that settings loaded correctly.
- Split the builds so the startup and EEPROM changes can be tested without the
  new ProUI host activity and UBL probe reporting. The full development branch
  keeps those features.

## Verified on one printer

- The previous candidate stopped at the Creality splash and did not answer over
  USB. The official January image recovered the printer. The R2 diagnostic
  build then booted and answered OctoPrint at 250000 baud.
- EEPROM validated. The settings report matched the recovered printer, the
  Z offset was retained, and all 81 points in saved mesh slot 0 matched the
  pre-update backup. Slot 1 was selected at startup; slot 0 was loaded in RAM
  for comparison. There was no EEPROM save, heating, homing, or print.
- The exact cause of the previous failure is still unknown. The full R2 build
  compiled and passed offline image checks. Its boot, printing, and display
  behavior have not been tested on hardware.

## Build the diagnostic version

Use the source tagged `s1-f1-r2-diagnostic`, based on
MRiscoC `cbb4c9863f648319b1d97a54176057f449e9caa8`. It includes the
generated Ender3S1 / 301F1 / BLT / UBL / MPC configuration from Special
Configurations `96a61d804810a987a081a0435eaa441e97630a9f`.
Run `pio run -e STM32F103RC_creality` with the standard target flags.
The tested build used Arduino STM32 1.9.0 and ARM GCC 9.2.1. The binary
embeds its build date, so a fresh build can have a different hash.

For this experiment, use only an Ender-3 S1 with the matching STM32F103
Creality v301 mainboard and ProUI/UBL/MPC configuration. Back up settings
and meshes and keep the known-good January image before testing. Do not
use this target for STM32F4 or other boards. See the
[upstream installation guide](https://github.com/mriscoc/Ender3V2S1/wiki/How-to-install-the-firmware#manual-installation-for-ender3-v2-and-s1-with-stm32f1-soc).
