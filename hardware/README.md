# Hardware Notes

## E73-2G4M04S1A

本项目使用 Ebyte E73-2G4M04S1A。按官方资料，该模块基于 Nordic nRF52810。

开发时不要把它当 Nordic DK 使用。官方 SDK 的 `pca10040e` 只用于选择 nRF52810 相关工程配置，实际硬件引脚以 E73 模块和底板接线为准。

## 必须确认的接线

### SWD

```text
VCC
GND
SWDIO
SWDCLK
RESET 可选
```

### GPIO 运行证明

记录一个外接 LED 或可测量 GPIO：

```text
GPIO:
连接方式:
高电平是否点亮:
串联电阻:
```

### 当前 MVP 硬件

```text
供电: USB，暂不做电源开关
状态 LED: P0.17，已验证可控
查找 LED: P0.18，已验证可控
按钮: 板载 SW1/SW2，GPIO 扫描已确认 SW1=P0.14，SW2=P0.13
蜂鸣器: 无源蜂鸣器，待接线；需要 PWM 方波驱动
```

### 板载按钮扫描记录

2026-06-14 使用临时 GPIO 扫描函数，通过 BLE Notify 输出 `button=P0.xx`。

当前观测：

```text
SW1: P0.14
SW2: P0.13
```

说明：

- 该记录来自当前 EWT73 测试板实测，不来自 Nordic DK 默认 `pca10040e` 板级定义。
- `P0.06` / `P0.08` 暂不作为按键脚使用，资料显示它们更可能与 USB 转串口 TXD/RXD 跳帽相关。
- `P0.17` / `P0.18` 已用于板载 LED。
- `P0.21` 可能涉及 reset，未纳入按键扫描。

## 待补充

- E73 引脚图
- 底板原理图
- 下载器型号
- 无源蜂鸣器接线 GPIO 和驱动方式
