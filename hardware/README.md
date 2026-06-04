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

### GPS UART

```text
GPS_TX -> nRF52810_RX:
GPS_RX -> nRF52810_TX:
GPS_GND -> nRF52810_GND:
GPS_VCC:
GPS 波特率:
```

## 待补充

- E73 引脚图
- 底板原理图
- GPS 模块型号
- 下载器型号
