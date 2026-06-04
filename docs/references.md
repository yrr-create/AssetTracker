# References

## 官方资料

- Ebyte E73-2G4M04S1A 官方产品页：<https://www.ebyte.com/product/305.html>
- Nordic nRF5 SDK 文档：<https://docs.nordicsemi.com/r/bundle/nrf5_SDK_v17.1.1/page/index.html>
- Nordic Developing for nRF52810：<https://docs.nordicsemi.com/r/bundle/nrf5_sdk_v15.2.0/page/nrf52810_user_guide.html>
- Nordic UART/Serial Port Emulation over BLE：<https://docs.nordicsemi.com/r/bundle/nrf5_SDK_v17.1.1/page/ble_sdk_app_nus_eval.html>
- Nordic S112 SoftDevice：<https://www.nordicsemi.com/Products/Development-software/S112>
- Nordic nRF52810 Get Started：<https://www.nordicsemi.com/Products/nRF52810/GetStarted>
- Nordic Academy BLE Fundamentals：<https://academy.nordicsemi.com/courses/bluetooth-low-energy-fundamentals/>

## 本项目优先阅读顺序

1. `Developing for nRF52810`：理解 `pca10040e` 和 nRF52810 内存限制。
2. `UART/Serial Port Emulation over BLE`：理解 BLE UART/NUS 示例，后续 GPS 坐标就从这里发。
3. `S112 SoftDevice`：确认协议栈方向和烧录顺序。
4. Ebyte E73 资料：确认模块供电、SWD、引脚和天线版本。
5. GPS 模块手册：确认 UART 波特率、电平、NMEA 输出语句。

## 不建议一开始深读

- 完整 BLE 课程：等 BLE UART 跑通后再看，效率更高。
- nRF Connect SDK/Zephyr 工程代码：本项目初期走 nRF5 SDK，不要混用工程结构。
