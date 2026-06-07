# BLE Warehouse Asset Tag MVP Design

## Product Direction

LocationGET is shifting from a GPS coordinate demo to a BLE warehouse asset tag prototype. The target scenario is reusable warehouse assets such as tools, scanners, bins, sample boxes, or lab equipment. Each asset carries an nRF52810 BLE tag. A phone scans nearby tags, shows RSSI strength, records last-seen state, and can connect to a selected tag to trigger LED/buzzer identification.

This is closer to real warehouse asset management than fake GPS data because the tag does not claim to know its own coordinates. Location is inferred by the scanning phone or gateway through last-seen area, scan time, and signal strength.

## MVP Scope

The next completed version should demonstrate:

- Phone scans BLE tags named `L4-*`.
- The selected asset shows RSSI as near/middle/far.
- Phone connects to `L4-001`.
- Phone sends `FIND_ON`; the tag enters finding mode.
- Phone sends `FIND_OFF` or the board button is pressed; the tag exits finding mode.
- Phone sends `STATUS?`; the tag returns ID, battery placeholder, and current state.
- Documentation explains current hardware limits and next product steps.

## Hardware

Current MVP hardware:

- Ebyte E73-2G4M04S1A / nRF52810 board.
- USB power for bring-up.
- Board LED on `P0.17` as status LED.
- Board LED on `P0.18` as finding LED.
- Board button as stop/confirm input after its GPIO is confirmed.
- Passive buzzer prepared for the next step. It needs PWM, not a static GPIO high/low level.

Power switch is out of scope for the MVP because the board is currently powered by USB.

## Firmware Protocol

BLE UART / Nordic UART Service remains the first control channel because it has already been validated with nRF Connect.

Commands sent from phone to tag:

```text
FIND_ON
FIND_OFF
STATUS?
```

Notifications sent from tag to phone:

```text
id=L4-001,bat=100,state=normal
id=L4-001,bat=100,state=finding
```

`bat=100` is a placeholder until real battery measurement is added.

## App Direction

nRF Connect is enough for protocol verification, but it cannot provide real asset records. The custom Android app is needed for:

- Asset list and BLE scan filtering.
- RSSI display and near/middle/far classification.
- Last seen time and last seen area.
- Asset binding such as `L4-001 -> Electric Drill -> Shelf A3`.
- Find button that sends `FIND_ON` / `FIND_OFF`.
- Inventory view showing found, missing, and low-battery assets.

First Android version should be native Android rather than Flutter, because BLE permissions, scanning, connection, GATT write, and RSSI handling are more direct.

## Data Ownership

The tag should store only low-frequency device configuration in the future, such as device ID and settings. Dynamic management data should live in the phone app or backend:

- Last seen time.
- Last seen RSSI.
- Last seen area.
- Asset name.
- Shelf or zone.
- Inventory records.

This avoids unnecessary nRF52810 Flash writes and matches the fact that location/RSSI are measured by the scanner, not by the tag.

## Next Week Plan

Day 1:

- Update docs from GPS route to BLE warehouse asset tag route.
- Confirm command protocol and MVP definition.

Day 2:

- Implement `FIND_ON` / `FIND_OFF` in `nus_data_handler()`.
- Use P0.17/P0.18 for visual finding behavior.
- Verify with nRF Connect.

Day 3:

- Implement `STATUS?`.
- Confirm board button GPIO.
- Make button stop finding mode.

Day 4:

- Connect passive buzzer safely.
- Use PWM to generate sound.
- Make `FIND_ON` trigger LED + buzzer.

Day 5:

- Start Android app MVP.
- Scan BLE devices, filter `L4-*`, show RSSI.

Day 6:

- Connect to selected tag.
- Send `FIND_ON`, `FIND_OFF`, and `STATUS?`.
- Show asset details and last seen data.

Day 7:

- Polish README, progress, troubleshooting, and patch files.
- Push GitHub.
- Record demo screenshots/video.

## Success Criteria

The project is considered complete for this MVP when a phone can scan `L4-001`, display RSSI, open an asset detail screen, trigger LED/buzzer finding, stop finding by App or button, and read a status response from the tag.
