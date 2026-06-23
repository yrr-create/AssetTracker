# AssetTracker

AssetTracker 是一个基于 Ebyte E73-2G4M04S1A / Nordic nRF52810 的 BLE 仓库资产标签原型。

手机可以扫描标签、连接标签、查看状态、触发查找，标签端可以用 LED 和蜂鸣器提示位置，并上报温湿度等环境信息。

## 当前进度

已经完成：

- BLE 广播和连接，手机端用 nRF Connect 可以发现并连接设备。
- 基于 Nordic UART Service 的命令收发。
- `findon` / `findoff` / `s?` 三个基础命令。
- P0.18 查找灯控制。
- P0.15 无源蜂鸣器控制，查找状态下持续间歇鸣叫。
- 板载按钮关闭查找状态。
- AHT20 温湿度读取。
- 环境状态判断：`normal`、`warning`、`alarm`。
- 环境报警逻辑：warning 只上报，alarm 在非查找状态下触发状态灯和蜂鸣器。

计划继续做：

- 接入 GY-SGP 气体 / VOC 传感器，用来补充环境风险判断。
- 做一个 Android App，替代 nRF Connect，提供资产列表、RSSI 远近、详情页和查找按钮。
- 后续再考虑电池供电和低功耗优化。

## 硬件

当前使用的硬件：

| 模块 | 用途 |
| --- | --- |
| Ebyte E73-2G4M04S1A | BLE 主控模块，芯片为 nRF52810 |
| EWT73 测试底板 | 引出 GPIO、USB 供电、按键、LED、SWD |
| AHT20 | 温湿度传感器，I2C 通信 |
| 无源蜂鸣器 | 查找和报警提示 |
| GY-SGP | 气体 / VOC 传感器，后续扩展 |
| J-Link | SWD 下载和调试 |

注意：项目里用到 Nordic SDK 的 `pca10040e` 工程配置，是因为 Nordic 官方用这个配置支持 nRF52810 示例。它不代表本项目使用 Nordic PCA10040E 开发板。实际 LED、按钮、蜂鸣器、传感器都按 EWT73 底板接线来确认。

## 当前接线

### AHT20

| AHT20 | EWT73 |
| --- | --- |
| VIN | 3V3 |
| GND | GND |
| SCL | P0.12 |
| SDA | P0.11 |

### 蜂鸣器

| 蜂鸣器 | EWT73 |
| --- | --- |
| + | P0.15 |
| - | GND |

### 板载资源

| 功能 | GPIO |
| --- | --- |
| 状态灯 | P0.17 |
| 查找灯 | P0.18 |
| SW1 | P0.14 |
| SW2 | P0.13 |

PWR 灯是电源指示灯，不由固件控制。

## BLE 命令

当前先用 nRF Connect for Mobile 手动验证，后续 App 会把这些命令做成按钮和页面。

| 命令 | 作用 |
| --- | --- |
| `findon` | 进入查找状态，P0.18 闪烁，蜂鸣器间歇鸣叫 |
| `findoff` | 退出查找状态，关闭查找灯和蜂鸣器 |
| `s?` | 返回当前资产状态 |

## 状态返回

当前返回格式是文本，方便用 nRF Connect 直接观察：

```text
id=L4-001,bat=100,state=normal,aht=ok,temp=32.2,hum=73.8,env=warning
```

字段说明：

| 字段 | 含义 |
| --- | --- |
| `id` | 资产标签编号 |
| `bat` | 电量占位值，目前还没有真实电池采样 |
| `state` | 标签状态：`normal` 或 `finding` |
| `aht` | AHT20 状态：`ok` 或 `fail` |
| `temp` | 温度，单位摄氏度 |
| `hum` | 相对湿度，百分比 |
| `env` | 环境状态：`normal`、`warning`、`alarm` |

如果 AHT20 没接好或没有供电，会返回：

```text
id=L4-001,bat=100,state=normal,aht=fail
```

## 环境报警策略

当前阈值先写在固件里，后续 App 可以做成可配置项。

| 状态 | 条件 | 设备表现 |
| --- | --- | --- |
| `normal` | 温湿度在正常范围 | 不额外提示 |
| `warning` | 接近异常范围 | 只通过 BLE 状态字段提醒 |
| `alarm` | 温度或湿度达到报警阈值 | 非查找状态下，P0.17 快闪，蜂鸣器低频报警 |

查找状态优先级最高。也就是说，用户正在找标签时，设备优先执行 `findon` 的灯光和蜂鸣器节奏；环境报警不会抢占查找提示。

当前阈值：

```text
温度 warning: >= 35.0 C
温度 alarm:   >= 45.0 C
湿度 warning: >= 75.0 %
湿度 alarm:   >= 85.0 %
```

## 固件工程

本仓库不提交完整 Nordic SDK。克隆项目后，需要自己准备：

- nRF5 SDK 17.1.0
- S112 SoftDevice v7.2.0
- SEGGER Embedded Studio
- Nordic Command Line Tools
- J-Link 驱动

当前本机 SDK 路径：

```text
G:\Personalportfolio\NordicSDK\nRF5_SDK_17.1.0_ddde560
```

当前固件工程基于：

```text
G:\Personalportfolio\NordicSDK\nRF5_SDK_17.1.0_ddde560\examples\ble_peripheral\ble_app_uart\pca10040e\s112\ses\ble_app_uart_pca10040e_s112.emProject
```

烧录脚本在仓库根目录：

```powershell
.\burn.bat
```

如果第一次烧 BLE 工程，需要先烧 S112 SoftDevice，再烧应用程序。后续只改应用程序时，通常烧应用 hex 并 reset 即可。

## 目录说明

```text
docs/       项目进度、调试记录、问题复盘
firmware/   固件说明、关键修改记录
hardware/   接线、板卡、传感器说明
notes/      临时笔记和学习记录
environment/ 工具链和环境安装记录
```

## 目前状态

这个项目已经完成 BLE 资产标签的核心闭环：

1. 手机能发现并连接标签。
2. 手机能发送查找命令。
3. 标签能用灯和蜂鸣器响应查找。
4. 标签能读取 AHT20 温湿度。
5. 标签能根据温湿度返回环境状态并触发报警。

下一步重点是把手机端 App 做出来，让这个原型从 nRF Connect 验证工具变成一个真正能演示的仓库资产管理界面。
