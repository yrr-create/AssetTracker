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
按钮: 板载按钮，GPIO 待确认
蜂鸣器: 无源蜂鸣器，待接线；需要 PWM 方波驱动
```

## 待补充

- E73 引脚图
- 底板原理图
- 下载器型号
- 板载按钮对应 GPIO
- 无源蜂鸣器接线 GPIO 和驱动方式
