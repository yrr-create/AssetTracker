# nRF Connect Evidence

This folder stores phone-side screenshots used as project evidence for the
AssetTracker BLE firmware bring-up.

## Timeline

- `2026-06-10_nrfconnect_ble-uart-hello.jpg` - BLE UART baseline: phone connects to L4 and receives the first test string.
- `2026-06-18_nrfconnect_status-aht-ok-temp28-hum66.jpg` - Early `s?` status response with AHT20 temperature and humidity.
- `2026-06-18_nrfconnect_status-aht-ok-temp28-hum68.jpg` - Repeated AHT20 status read through NUS.
- `2026-06-18_nrfconnect_aht-fail-basic-status.jpg` - AHT20 failure state during debugging.
- `2026-06-18_nrfconnect_aht-fail-stage4-raw.jpg` - AHT20 debug output with stage and raw bytes.
- `2026-06-18_nrfconnect_aht-fail-stage4-squery.jpg` - AHT20 stage-4 failure captured after `s?`.
- `2026-06-18_nrfconnect_aht-fail-stage4-repeat.jpg` - Repeated stage-4 failure for comparison.
- `2026-06-18_nrfconnect_findoff-aht-fail-stage4.jpg` - `findoff` command while AHT20 was still failing.
- `2026-06-18_nrfconnect_findon-aht-negative-temp.jpg` - `findon` command with invalid negative AHT20 reading.
- `2026-06-18_nrfconnect_aht-fail-after-retest.jpg` - Retest screenshot showing AHT20 failure before wiring/code recovery.
- `2026-06-21_nrfconnect_env-warning-temp32-hum74.jpg` - Environment warning status with temperature and humidity.
- `2026-06-22_nrfconnect_l4-advertising-rssi.jpg` - Scanner view proving L4 advertising and RSSI visibility.
- `2026-06-22_nrfconnect_i2c-scan-aht20-only-0x38.jpg` - I2C scan before GY-SGP is detected: AHT20 at `0x38`.
- `2026-06-22_nrfconnect_i2c-scan-aht20-gysgp-0x38-0x59.jpg` - I2C scan after GY-SGP wiring/probe fix: AHT20 `0x38`, GY-SGP `0x59`.
- `2026-06-22_nrfconnect_sgp40-first-raw-65529.jpg` - First SGP40 raw read after bring-up. This was treated as a startup value, not an air-quality decision.
- `2026-06-22_nrfconnect_sgp40-stable-raw-29103.jpg` - SGP40 raw value after it settled.
- `2026-06-22_nrfconnect_sgp40-stable-raw-29655.jpg` - Repeated SGP40 raw read in the same desk environment.
- `2026-06-22_nrfconnect_sgp40-stable-raw-29692.jpg` - Repeated SGP40 raw read in the same desk environment.
- `2026-06-22_nrfconnect_sgp40-stable-raw-29746.jpg` - Repeated SGP40 raw read in the same desk environment.
- `2026-06-22_nrfconnect_sgp40-stable-raw-29817.jpg` - Repeated SGP40 raw read in the same desk environment.
- `2026-06-22_nrfconnect_sgp40-crc-pass-raw-28928.jpg` - SGP40 raw read after CRC check was added.
