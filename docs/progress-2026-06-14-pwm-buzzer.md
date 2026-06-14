# 2026-06-14 PWM buzzer progress

## Current milestone

The buzzer finding alert has been moved from GPIO delay-loop sound generation to the nRF52 PWM driver.

Verified build:

```text
emBuild.exe -config Release -rebuild ble_app_uart_pca10040e_s112.emProject
```

Result: Release build passed.

## PWM driver setup

Config file:

```text
G:\Personalportfolio\NordicSDK\nRF5_SDK_17.1.0_ddde560\examples\ble_peripheral\ble_app_uart\pca10040e\s112\config\sdk_config.h
```

Required switches:

```c
#define NRFX_PWM_ENABLED 1
#define NRFX_PWM0_ENABLED 1
#define PWM_ENABLED 1
#define PWM0_ENABLED 1
```

SES project file:

```text
G:\Personalportfolio\NordicSDK\nRF5_SDK_17.1.0_ddde560\examples\ble_peripheral\ble_app_uart\pca10040e\s112\ses\ble_app_uart_pca10040e_s112.emProject
```

Required source file added under `nRF_Drivers`:

```text
modules\nrfx\drivers\src\nrfx_pwm.c
```

## Buzzer PWM parameters

Current buzzer pin:

```text
P0.15
```

Current PWM settings:

```text
base_clock = NRF_PWM_CLK_1MHz
top_value  = 500
duty value = 250
```

Meaning:

```text
1,000,000 / 500 = 2,000 Hz
250 / 500 = 50% duty
```

The 2kHz tone sounded more normal on the current passive buzzer than the earlier 4kHz test.

## Finding alert rhythm

Current product behavior:

```text
findon  -> finding state
finding -> PWM on 150ms, PWM off 150ms, repeat
findoff -> normal state, PWM off, P0.18 off
SW1/SW2 while finding -> normal state, PWM off, P0.18 off
```

Design decision:

```text
PWM controls tone.
The finding loop controls rhythm.
```

This keeps the tone generation out of the CPU delay-loop implementation while still giving a clear intermittent warehouse finding alert.

