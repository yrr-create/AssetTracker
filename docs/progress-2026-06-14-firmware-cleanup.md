# 2026-06-14 Firmware cleanup progress

## Decision

The official `main.c` should keep only product behavior.

Debug history is kept in documentation and patch files instead of staying in the running firmware source.

## Kept in product firmware

```text
l4_leds_init()
l4_buzzer_init()
l4_buzzer_find_pattern()
l4_buttons_init()
l4_buttons_process()
l4_set_finding()
l4_finding_process()
l4_send_status()
l4_command_equals()
```

## Removed from product firmware

Temporary GPIO/button scan helpers:

```text
m_l4_button_test_pins[]
m_l4_last_button_mask
l4_button_scan_init()
l4_button_scan_process()
l4_send_button()
```

Temporary buzzer bring-up helper:

```text
L4_BUZZER_TEST_MS
l4_buzzer_test_beep()
```

The scan and boot-beep steps remain recorded in:

```text
docs/progress-2026-06-14-ble-command-button-stop.md
docs/progress-2026-06-14-buzzer-finding.md
firmware/patches/
```

## Protocol decision

The project protocol is intentionally kept simple for nRF Connect testing:

```text
findon
findoff
s?
```

The earlier uppercase command names were design placeholders. Documentation has been updated to match the actual firmware commands.

## Product behavior after cleanup

```text
findon  -> finding state, P0.18 blinks, P0.15 buzzer repeats
findoff -> normal state, P0.18 off, P0.15 buzzer off
SW1/SW2 while finding -> normal state, P0.18 off, P0.15 buzzer off
s?      -> Notify current state
```

