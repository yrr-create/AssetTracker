# 2026-06-21 Temperature and Humidity Alarm

## Goal

Add an environment warning/alarm layer on top of the verified AHT20
temperature and humidity readings.

The firmware keeps using BLE UART/NUS and nRF Connect for verification.

## Default thresholds

The firmware stores temperature and humidity values as fixed-point values
multiplied by 10.

```c
#define L4_TEMP_WARN_X10   350   // 35.0 C
#define L4_TEMP_ALARM_X10  450   // 45.0 C
#define L4_HUM_WARN_X10    750   // 75.0 %
#define L4_HUM_ALARM_X10   850   // 85.0 %
```

Behavior:

```text
env=normal   -> temperature < 35.0 C and humidity < 75.0 %
env=warning  -> temperature >= 35.0 C or humidity >= 75.0 %
env=alarm    -> temperature >= 45.0 C or humidity >= 85.0 %
```

These are default firmware thresholds. A future mobile app can make thresholds
configurable by asset type.

## BLE status format

When AHT20 reads successfully, the `s?` command returns:

```text
id=L4-001,bat=100,state=normal,aht=ok,temp=32.2,hum=73.8,env=normal
id=L4-001,bat=100,state=normal,aht=ok,temp=35.1,hum=73.8,env=warning
id=L4-001,bat=100,state=normal,aht=ok,temp=45.0,hum=73.8,env=alarm
```

When AHT20 fails, the firmware returns `aht=fail` and does not trigger an
environment alarm from invalid sensor data.

## Local alert behavior

Priority:

```text
finding > env alarm > env warning > env normal
```

- `finding`: P0.18 finding LED and buzzer use the existing fast find pattern.
- `env=alarm`: when not finding, P0.17 status LED flashes and the buzzer beeps
  at a low-frequency alarm rhythm.
- `env=warning`: status is reported over BLE only. No LED or buzzer alert.
- `env=normal`: quiet state.

Important implementation note: while `m_l4_env_state == L4_ENV_ALARM`, the main
loop must not call `idle_state_handle()` immediately after the alarm step,
otherwise `nrf_pwr_mgmt_run()` can put the MCU into idle and stop the repeated
local alert rhythm.

## Verification

Verified with nRF Connect:

1. Temporarily lowered `L4_TEMP_ALARM_X10` to force `env=alarm`.
2. Confirmed BLE status included `env=alarm`.
3. Confirmed alarm alert repeated continuously instead of only beeping twice.
4. Restored the standard default threshold values above.
