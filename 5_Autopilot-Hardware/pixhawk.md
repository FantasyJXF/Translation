# Pixhawk 硬件

Pixhawk 是 PX4 飞行堆栈的标准微控制器平台。它在 NuttX 操作系统上运行 PX4 中间件。依照CC-BY-SA 3.0 许可开放硬件设计，所有图纸和设计文件都是可用的。Pixfalcon 是 Pixhawk 的FPV 和类似平台较小版本。 对于无人机性能要求较高或照相，高通晓龙可能会更加合适。


##快速摘要

-  主片上系统: STM32F437
    - CPU: 180 MHz，ARM Cortex M4内核，单精度FPU
    - RAM: 256 KB SRAM
-   失效保护片上系统: STM32F100
    - CPU: 24 MHz ARM Cortex M3
    - RAM: 8 KB SRAM
-   Wifi: 外加ESP8266 
    - GPS: U-Blox 7/8 (Hobbyking) / U-Blox 6 (3D Robotics)
    - 光流: PX4 光流模块
    - 可购买:
      - Hobbyking EU 版本 (433 MHz)
-   Hobbyking US 版本(915 MHz)
    - 3D Robotics 仓储 (GPS 和数传不捆绑)
-   配件:
    - 数字空速管
    - Hobbyking Wifi 数传
    - Hobbyking OSD + US数传 (915 MHz)
    - Hobbyking OSD + EU 数传 (433 MHz)

## 接口

- 一个 I2C
- 一个CAN (2x 可选)
  - 一个 ADC
  - 四个 UART (二个给光流控制)
  - 一个 Console口
  - 八路 PWM 用来做手动控制
  - 六路 PWM / GPIO / PWM 输入
  - S.BUS / PPM / Spektrum 输入
  - S.BUS 输出

## 引出线和原理图

板子信息详细记录 Pixhawk 的项目网站。
