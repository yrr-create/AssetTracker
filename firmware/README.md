# Firmware Notes

## 基准工程

亮灯/GPIO：

```text
nRF5_SDK_17.1.0_ddde560\examples\peripheral\blinky\pca10040e\blank
```

SEGGER Embedded Studio 中要打开的 solution 文件是：

```text
nRF5_SDK_17.1.0_ddde560\examples\peripheral\blinky\pca10040e\blank\ses\blinky_pca10040e.emProject
```

BLE UART：

```text
nRF5_SDK_17.1.0_ddde560\examples\ble_peripheral\ble_app_uart\pca10040e\s112
```

SEGGER Embedded Studio 中要打开的 solution 文件是：

```text
nRF5_SDK_17.1.0_ddde560\examples\ble_peripheral\ble_app_uart\pca10040e\s112\ses\ble_app_uart_pca10040e_s112.emProject
```

注意：SEGGER Embedded Studio 使用 `.emProject` 作为工程/solution 文件，不是 Visual Studio 的 `.sln`。窗口标题显示 `blinky_pca10040e - SEGGER Embedded Studio` 时，说明已经打开了 `blinky_pca10040e` 这个 solution。

## 建议策略

先不要把完整 SDK 复制进本仓库。SDK 体积大，且属于第三方依赖。建议记录 SDK 下载版本和工程路径，后续只提交自己修改过的项目副本或补丁。

正式开始写固件时，可以建立：

```text
firmware/location_tracker_nrf52810/
```

从 `ble_app_uart\pca10040e\s112` 复制最小工程，再把工程内 SDK 相对路径调整为本机 SDK 路径或仓库约定的 SDK 路径。这个步骤会生成本项目自己的 SES solution，目前还没有做。

后续在自己的 solution 中逐步加入：

1. 自定义设备名
2. 固定测试字符串发送
3. GPS UART 接收
4. NMEA 行缓冲
5. `$GPRMC` / `$GNRMC` 解析
6. BLE UART 坐标发送

## 烧录顺序

BLE UART 阶段需要：

1. Erase chip
2. Program S112 SoftDevice hex
3. Program application hex
4. Reset

亮灯 `blank` 阶段不需要 SoftDevice。
