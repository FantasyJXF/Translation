# Pixfalcon

# Pixfalcon硬件

Pixfalcon 是衍生于 Pixhawk 的设计，优化在空间受限的应用，如 FPV 。它有较少的IO，以便尺寸的减少。对于无人机性能要求较高或带有照相机，高通晓龙可能是一个会更加合适。



## 快速摘要

-  主片上系统: STM32F437
    - CPU: 180 MHz，ARM Cortex M3内核，单精度FPU
    - RAM: 256 KB SRAM 
-   失效保护片上系统: STM32F100
    - CPU: 24 MHz ARM Cortex M3
    - RAM: 8 KB SRAM
-   GPS: U-Blox M8 (捆绑)
    - 光流: PX4 Flow unit
    - Availability: Hobbyking Store
    - 数字空速传感器
    -集成测量显示在屏幕上:
      - Hobbyking OSD + US Telemetry (915 MHz)
-   Hobbyking OSD + EU Telemetry (433 MHz)
-   纯测量方案:
    - Hobbyking Wifi Telemetry
    - Hobbyking EU Micro Telemetry (433 MHz)
    - [Hobbyking US Micro Telemetry (915 MHz)

## 接口

- 一个I2C
- 二个UART (一个给数传 / OSD, 没有光流控制)
  - 八路 PWM 用来做手动操作
  - S.BUS / PPM 输入
