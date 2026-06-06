# LocationGET

Ebyte E73-2G4M04S1A / nRF52810 location prototype.

目标是先做一个可验证的蓝牙定位样机：E73-2G4M04S1A 模块运行 nRF52810 固件，后续接 GPS 模块，通过 BLE UART 把坐标字符串发到手机。初期不用正式 App，先用 nRF Connect 验证。

## 当前硬件

- BLE 模块：Ebyte E73-2G4M04S1A
- SoC：Nordic nRF52810
- SDK：nRF5 SDK 17.1.0/17.1.1 路线
- SoftDevice：S112 v7.2.0
- 手机验证工具：nRF Connect for Mobile
- 固件工程基准：Nordic SDK 的 `pca10040e` 示例

注意：`pca10040e` 是 Nordic SDK 为 nRF52810 提供的示例配置，不代表本项目使用 Nordic PCA10040E 开发板。E73 是模块，LED、按键、GPS UART、供电和 SWD 都要按自己的底板接线确认。

## 本地 SDK 准备

本仓库不提交完整 Nordic SDK。别人克隆项目后，需要自己下载并解压：

```text
nRF5 SDK 17.1.0
S112 SoftDevice v7.2.0
```

推荐把 SDK 固定放到仓库外的稳定目录：

```text
G:\Personalportfolio\NordicSDK\nRF5_SDK_17.1.0_ddde560
```

当前电脑已经可以使用这个路径。不要把工程长期指向 `C:\Users\Administrator\Documents\Codex\...` 下面的临时附件目录；那个目录只适合临时验证，不适合作为项目依赖。

在 SEGGER Embedded Studio 中打开 blinky 时，完整路径应类似：

```text
G:\Personalportfolio\NordicSDK\nRF5_SDK_17.1.0_ddde560\examples\peripheral\blinky\pca10040e\blank\ses\blinky_pca10040e.emProject
```

## 五关开发路线

### 第 1 关：编译通过

目标：确认 SDK、工程路径、芯片目标和工具链正确。

先编译亮灯工程：

```text
examples\peripheral\blinky\pca10040e\blank\ses\blinky_pca10040e.emProject
```

不要打开 DeepSeek 提到的这个路径，因为官方 SDK 中不存在：

```text
examples\peripheral\blinky\pca10040e\s112\ses
```

如果用 GCC Makefile 构建 BLE UART，Windows 上建议使用：

```bash
make ABSOLUTE_PATHS=1 PASS_INCLUDE_PATHS_VIA_FILE=1
```

如果使用较新的 GCC 出现 `array-bounds` 被 `-Werror` 放大的问题，可以临时降级该告警；正式开发更推荐安装 Nordic 旧 SDK 兼容的 GNU Arm Embedded Toolchain 9-2020-q2，或使用 SEGGER Embedded Studio 5.42a 附近版本。

### 第 2 关：能连接和烧录芯片

目标：确认 SWD 和供电可靠。

需要接线：

```text
VCC
GND
SWDIO
SWDCLK
RESET 可选
```

工具可选：

- SEGGER Embedded Studio 的 Target 菜单
- nRF Connect Programmer
- Nordic Command Line Tools + J-Link

如果没有 J-Link 或 nRF DK 当下载器，这一关会卡住。普通 USB-TTL 不能直接替代 SWD 下载器。

### 第 3 关：证明程序在跑

目标：不要只依赖官方板载 LED 假设。

E73 模块不等于 Nordic DK，官方 blinky 的 LED 引脚可能没有接任何东西。可选验证方式：

- 外接 LED 到一个确认的 GPIO，再修改 `boards.h` 或工程板级引脚定义
- 用示波器/万用表看 GPIO 翻转
- 用 RTT/log 或断点确认主循环运行

本关完成标准：复位后能观察到一个由固件控制的 GPIO 行为。

### 第 4 关：BLE UART 跑通

目标：手机能扫到设备、连接、收发文本。

使用工程：

```text
examples\ble_peripheral\ble_app_uart\pca10040e\s112\ses\ble_app_uart_pca10040e_s112.emProject
```

这一步需要先烧 S112 SoftDevice，再烧 application hex。手机端用 nRF Connect 或 Nordic UART 类工具连接。BLE UART 的核心发送函数是：

```c
ble_nus_data_send(&m_nus, data_array, &length, m_conn_handle);
```

本关完成标准：手机能看到设备广播，连接后能看到从固件发来的测试字符串。

### 第 5 关：GPS 坐标上报

目标：GPS 模块输出 NMEA，nRF52810 解析 `$GPRMC` 或 `$GNRMC`，通过 BLE UART 发坐标字符串到手机。

接线原则：

```text
GPS_TX -> nRF52810_RX
GPS_RX -> nRF52810_TX
GPS_GND -> nRF52810_GND
GPS_VCC -> 确认电压兼容
```

第一版只发送文本：

```text
lat=22.54321,lng=113.98765
```

本关完成标准：手机 nRF Connect 中能看到稳定更新的经纬度字符串。

## 建议时间周期

| 阶段 | 目标 | 预计时间 | 完成标准 |
| --- | --- | --- | --- |
| 第 1 关 | 编译通过 | 0.5-1 天 | 生成 hex/bin/out |
| 第 2 关 | SWD 烧录 | 0.5-1 天 | 能 erase/program/reset |
| 第 3 关 | GPIO 运行证明 | 0.5-1 天 | LED/GPIO/RTT 可观察 |
| 第 4 关 | BLE UART | 1-3 天 | 手机连接并收发文本 |
| 第 5 关 | GPS 坐标 | 2-5 天 | 手机看到坐标字符串 |
| 整理复盘 | 文档和问题记录 | 持续 | README/docs 同步更新 |

如果硬件接线、下载器或 GPS 供电不确定，时间会主要花在第 2、3、5 关。

## 推荐先读什么

不要先把所有课程看完。按遇到的问题读对应资料。

1. 做第 1-3 关时读：nRF52810 / `pca10040e` 说明，理解为什么不是 `pca10040`。
2. 做第 4 关时读：nRF5 SDK BLE UART/NUS 示例文档。
3. 做第 5 关时读：GPS 模块手册中的 UART、电平、波特率、NMEA `$GPRMC`/`$GNRMC`。
4. BLE 概念不懂时，再看 Nordic Academy BLE Fundamentals。该课程偏 nRF Connect SDK/Zephyr，概念可学，代码不要直接照搬到 nRF5 SDK。

详细链接见 [docs/references.md](docs/references.md)。

## 进度记录

每次推进后更新：

- [docs/progress.md](docs/progress.md)
- [docs/troubleshooting.md](docs/troubleshooting.md)

建议每完成一个小里程碑就提交一次 Git。

## 本地 Git 使用

```bash
git status
git add README.md docs hardware firmware notes
git commit -m "docs: initialize LocationGET project plan"
```

远程仓库需要 GitHub/Gitee 登录凭据。当前机器没有检测到 GitHub CLI，也没有可用 GitHub token。远程创建方式见 [docs/git-remote.md](docs/git-remote.md)。
