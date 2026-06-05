# Progress Log

用于记录每天做了什么、卡在哪里、下一步是什么。每次改完建议提交 Git。

## 2026-06-04

### 已确认

- 模块型号：Ebyte E73-2G4M04S1A
- 芯片方向：nRF52810
- SDK 方向：nRF5 SDK + S112
- 官方示例基准：`pca10040e`
- 项目路线：先 blinky，再 BLE UART，再 GPS 坐标上报

### 当前阶段

- 第 1 关：亮灯工程已编译通过
- 第 2 关：准备连接 ARM V12/J-Link 和 EWT73 SWD

### 下一步

1. 在 SEGGER Embedded Studio 中打开 `examples\peripheral\blinky\pca10040e\blank\ses\blinky_pca10040e.emProject`
2. ARM V12/J-Link 接 EWT73 的 `3V3/GND/SWDIO/SWCLK`
3. 执行 `Target -> Connect J-Link`
4. 如果连接成功，再执行 `Target -> Download blinky_pca10040e`
5. 如果连接失败，把完整 Target Output 复制到 `docs/troubleshooting.md`

### Solution 状态

- 当前使用 Nordic SDK 官方 solution：`blinky_pca10040e.emProject`
- 当前仓库还没有自己的 `location_tracker_nrf52810.emProject`
- 等第 2、3 关跑通后，再从 `ble_app_uart_pca10040e_s112.emProject` 建立本项目自己的 solution

## 进度模板

### YYYY-MM-DD

### 做了什么

- 

### 结果

- 

### 遇到的问题

- 

### 下一步

- 
