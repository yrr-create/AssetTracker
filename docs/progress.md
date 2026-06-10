# Progress Log

用于记录每天做了什么、卡在哪里、下一步是什么。每次改完建议提交 Git。

## 2026-06-04

### 已确认

- 模块型号：Ebyte E73-2G4M04S1A
- 芯片方向：nRF52810
- SDK 方向：nRF5 SDK + S112
- 官方示例基准：`pca10040e`
- 项目路线：先 blinky，再 BLE UART，再 BLE 仓库资产标签控制协议和 Android App

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

## 2026-06-07

### 做了什么

- 使用 `blinky_pca10040e.emProject` 完成编译、下载和运行验证。
- 修复 SES 8.28 对旧 SDK `flash_placement.xml` 中 `.text/.rodata size="0x4"` 的兼容问题。
- 将官方 blinky 从 `boards.h` / `bsp_board_led_invert()` 改为直接使用 `nrf_gpio.h` 控制 GPIO。
- 明确验证 EWT73 板上 `P0.17` 和 `P0.18` 可以由固件交替控制。

### 结果

- J-Link 连接成功时的关键输出：

```text
VTref=3.290V
Cortex-M4 identified.
```

- 烧录命令：

```powershell
nrfjprog --family NRF52 --program "G:\Personalportfolio\NordicSDK\nRF5_SDK_17.1.0_ddde560\examples\peripheral\blinky\pca10040e\blank\ses\Output\Debug\Exe\blinky_pca10040e.hex" --sectorerase --clockspeed 100
nrfjprog --family NRF52 --reset --clockspeed 100
```

- 烧录结果：

```text
Erase file - Done erasing
Program file - Done programming
Applying system reset.
Run.
```

- 观察结果：`P0.17` 和 `P0.18` 交替闪，说明第 3 关 GPIO 运行证明完成。

### 已保存的改法

- 当前本机 SDK 已修改：

```text
G:\Personalportfolio\NordicSDK\nRF5_SDK_17.1.0_ddde560\examples\peripheral\blinky\main.c
```

- 仓库内保存了可复现 patch：

```text
firmware\patches\blinky-ewt73-p017-p018.patch
```

### 下一步

- 进入第 4 关：基于 `ble_app_uart_pca10040e_s112.emProject` 做 BLE UART。
- 目标是手机 nRF Connect 能扫描到设备、连接设备，并收到固件发出的测试字符串。
- BLE UART 烧录必须先烧 S112 SoftDevice，再烧 application；它和前面的 blinky `blank` 工程不一样。

### 第 4 关 BLE UART 验证

- 手机 nRF Connect 已扫描到 `Nordic_UART`。
- 手机已连接成功，状态显示 `CONNECTED`。
- 已发现 `Nordic UART Service`：

```text
UUID: 6e400001-b5a3-f393-e0a9-e50e24dcca9e
```

- 已确认两个 characteristic：

```text
6e400002-b5a3-f393-e0a9-e50e24dcca9e  手机 -> 板子，Write/RX
6e400003-b5a3-f393-e0a9-e50e24dcca9e  板子 -> 手机，Notify/TX
```

- 在 `nus_data_handler()` 中处理 `BLE_NUS_EVT_COMM_STARTED`，手机开启 Notify 后，板子主动发送：

```text
hello from LocationGET
```

- 手机已收到该字符串，说明 BLE UART 的板子到手机方向已跑通。

仓库中保存了对应 patch：

```text
firmware\patches\ble-uart-send-hello-on-notify.patch
```

### 下一步

- 将设备名从 `Nordic_UART` 改成资产标签名，例如 `L4-001`。
- 做 `FIND_ON` / `FIND_OFF` / `STATUS?`，为仓库资产查找功能做准备。

### 项目方向调整：BLE 仓库资产标签

经过第 4 关 BLE UART 验证后，项目主线从“GPS 坐标上报”调整为“BLE 仓库资产标签系统”。原因：

- 当前 E73-2G4M04S1A / nRF52810 板子本身没有 GPS，继续发送经纬度只能是假数据。
- BLE 标签更适合真实仓库资产管理：扫描附近标签、记录 RSSI、最后出现时间和区域，并通过声光提醒查找实物。
- nRF Connect 只适合协议验证；真正的资产列表、最后出现记录、盘点和低电量提醒需要后续 Android App 实现。

当前 MVP 硬件选择：

```text
P0.17: 状态 LED
P0.18: 查找 LED
板载按钮: 停止查找/确认，GPIO 待确认
USB: 当前供电方式
无源蜂鸣器: 已准备，后续用 PWM 驱动
```

下一周目标：

```text
手机 App 扫到 L4-001
显示 RSSI 近/中/远
点击进入资产详情
点击查找后板子 LED 闪 + 蜂鸣器响
点击停止或按板子按钮后停止
App 能读取 STATUS 状态
```

已新增设计文档：

```text
docs\superpowers\specs\2026-06-07-ble-warehouse-asset-tag-mvp-design.md
```

## 2026-06-10

### 做了什么

- 在 BLE UART 示例中加入资产标签命令解析。
- 用 nRF Connect 向 NUS RX characteristic 写入 `FIND_ON` / `FIND_OFF` / `STATUS?`。
- 使用 NUS TX Notify 返回资产状态。
- 使用 `P0.18` 作为查找灯，确认当前底板 LED 为低电平亮。

### 结果

手机已验证：

```text
FIND_ON  -> id=L4-001,bat=100,state=finding
FIND_OFF -> id=L4-001,bat=100,state=normal
STATUS?  -> 返回当前 state
```

硬件已验证：

```text
PWR  = 电源指示灯，不由固件控制
P0.17 = 状态灯预留
P0.18 = 查找灯，FIND_ON 闪烁，FIND_OFF 熄灭
```

### 遇到的问题

- 一开始 `FIND_OFF` 后 P0.18 仍然亮。原因是底板 LED 是低电平亮，高电平灭。
- `idle_state_handle()` 会让芯片进入电源管理空闲状态；查找状态下如果每轮都进入 idle，P0.18 不能连续闪烁。当前主循环改为 finding 状态下执行 `l4_finding_process()`，非 finding 状态才进入 idle。

### 下一步

- 把手机开启 Notify 后的初始 hello 文本改成 `l4_send_status()`。
- 确认板载按钮对应 GPIO，并实现按钮停止查找。
- 连接无源蜂鸣器，用 PWM 实现查找声音。

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
