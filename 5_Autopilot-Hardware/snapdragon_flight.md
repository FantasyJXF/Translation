# 骁龙自动驾驶仪

骁龙Snapdragon Flight平台是高端的自动驾驶仪 / 机载计算机， 它的DSP 上 有QuRT 实时操作系统来运行 PX4 ，使用[DSPAL API](https://github.com/ATLFlight/dspal)兼容与 POSIX   。  与  [Pixhawk](../5_Autopilot-Hardware/pixhawk.md) 相比它添加了一个摄像头和 WiFi 及其高端的处理能力和不同的 IO 接口。

有关的骁龙平台飞行的更多信息在[Snapdragon-Flight-Details](https://www.intrinsyc.com/qualcomm-snapdragon-flight-details/)

 ![hardware-snapdragon](../pictures/hardware\hardware-snapdragon.jpg)

## 快速摘要

-                             片上系统: [骁龙 801](https://www.qualcomm.com/products/snapdragon/processors/801)
                                  - CPU: Quad-core 2.26 GHz Krait
                              - DSP: Hexagon DSP (QDSP6 V5A) – 801 MHz+256KL2 (运行飞控代码)
                              - GPU: Qualcomm® Adreno™ 330 GPU
                              - RAM: 2GB LPDDR3 PoP @931 MHz
-                             内存: 32GB eMMC Flash
                                  - 录像: 索尼 IMX135 on Liteon Module 12P1BAD11
                              - 4k@30fps 3840×2160 video capture to SD card with H.264 @ 100Mbits (1080p/60 with parallel FPV), 720p FPV
-                             光流: Omnivision OV7251 on Sunny Module MD102A-200
                                  - 640x480 @ 30/60/90 fps
-                             Wifi: Qualcomm® VIVE™ 1-stream 802.11n/ac with MU-MIMO † Integrated digital core
                                  - BT/WiFi: BT 4.0 and 2G/5G WiFi via QCA6234
                              - 802.11n, 2×2 MIMO with 2 uCOAX connectors on-board for connection to external antenna
-                             GPS: Telit Jupiter SE868 V2 module (使用外部UBLOX模块，建议由PX4代替)
                                  - uCOAX 连接器用于连接到外部 GPS 车载贴片天线
                              - CSR SiRFstarV @ 5Hz via UART
-                             加速度计 / 陀螺仪 /磁力计: Invensense公司的MPU-9250 9-轴传感器, 3x3mm QFN, 接在 SPI1
                                  - 气压计: Bosch公司的 BMP280 气压传感器, 接在 I2C3
                              - 电压: 5V直流电用外部2S-6S电池通过APM适配器调节至5V
                              - 可购买: [Intrinsyc商店](http://shop.intrinsyc.com/products/snapdragon-flight-dev-kit)

## 接口

-     一个USB 3.0 高速口 (micro-A/B)
-     微型 SD 卡插槽
      -万用接头 (PWB/GND/BLSP)
      - 电调接口 (2W UART)
      - I2C
      - 60针高速的Samtec QSH-030-01-L-D-A-K扩展连接器
        - 2x BLSP ([BAM 低速外设](http://www.inforcecomputing.com/public_docs/BLSPs_on_Inforce_6540_6501_Snapdragon_805.pdf))
-     USB

## 外置引线

<aside class="warning">
尽管晓龙使用 DF13 连接器，外置引线还是有别于 Pixhawk。
</aside>

### WiFi

- WLAN0, WLAN1 (+BT 4.0): U.FL connector: [Taoglas 胶粘剂的天线  (DigiKey公司)](http://www.digikey.com/product-detail/en/FXP840.07.0055B/931-1222-ND/3877414)

### 连接器

串口的默认对应如下所示︰

| 设备           | 描述                          |
| ---------------- | ------------------------------------ |
| ```/dev/tty-1``` | J15 (next to USB)                    |
| ```/dev/tty-2``` | J13 (next to power module connector) |
| ```/dev/tty-3``` | J12 (next to J13)                    |
| ```/dev/tty-4``` | J9 (next to J15)                     |

为自定义的 UART 到 BAM 映射，创建一个名为"blsp.config"文件和执行命令adb push 把他下到 ```/usr/share/data/adsp```。例如，保留默认的映射，你"blsp.config"应该看起来像:
tty-1 bam-9  
tty-2 bam-8  
tty-3 bam-6  
tty-4 bam-2  

#### J9 / GPS

| Pin  | 2-wire UART + I2C | SPI       | Comment       |
| ---- | ----------------- | --------- | ------------- |
| 1    | 3.3V              | 3.3V      | 3.3V          |
| 2    | UART2_TX          | SPI2_MOSI | Output (3.3V) |
| 3    | UART2_RX          | SPI2_MISO | Input (3.3V)  |
| 4    | I2C2_SDA          | SPI2_CS   | (3.3V)        |
| 5    | GND               | GND       |               |
| 6    | I2C2_SCL          | SPI2_CLK  | (3.3V)        |

#### J12 /云台总线

| Pin  | 2-wire UART + GPIO | SPI       | Comment       |
| ---- | ------------------ | --------- | ------------- |
| 1    | 3.3V               | 3.3V      |               |
| 2    | UART8_TX           | SPI8_MOSI | Output (3.3V) |
| 3    | UART8_RX           | SPI8_MISO | Input (3.3V)  |
| 4    | APQ_GPIO_47        | SPI8_CS   | (3.3V)        |
| 5    | GND                | GND       |               |
| 6    | APQ_GPIO_48        | SPI8_CLK  | (3.3V)        |

#### J13 / 电调总线

| Pin  | 2-wire UART + GPIO | SPI       | Comment     |
| ---- | ------------------ | --------- | ----------- |
| 1    | 5V                 | 5V        |             |
| 2    | UART6_TX           | SPI6_MOSI | Output (5V) |
| 3    | UART6_RX           | SPI6_MISO | Input (5V)  |
| 4    | APQ_GPIO_29        | SPI6_CS   | (5V)        |
| 5    | GND                | GND       |             |
| 6    | APQ_GPIO_30        | SPI6_CLK  | (5V)        |

#### J14 / 电源

| Pin  | Signal   | Comment     |
| ---- | -------- | ----------- |
| 1    | 5V DC    | Power input |
| 2    | GND      |             |
| 3    | I2C3_SCL | (5V)        |
| 4    | I2C3_SDA | (5V)        |

#### J15 / 无线接收器/传感器

| Pin  | 2-wire UART + I2C | SPI       | Comment |
| ---- | ----------------- | --------- | ------- |
| 1    | 3.3V              | 3.3V      |         |
| 2    | UART9_TX          | SPI9_MOSI | Output  |
| 3    | UART9_RX          | SPI9_MISO | Input   |
| 4    | I2C9_SDA          | SPI9_CS   |         |
| 5    | GND               | GND       |         |
| 6    | I2C9_SCL          | SPI9_CLK  |         |

## 外设

### UART to Pixracer / Pixfalcon 接线

这个接口是用来利用Pixracer/ Pixfalcon作为I / O接口板。连接到`TELEM1`在 Pixfalcon和在`TELEM2`在Pixracer。

| Snapdragon J13 Pin | Signal      | Comment              | Pixfalcon / Pixracer Pin |
| ------------------ | ----------- | -------------------- | ------------------------ |
| 1                  | 5V          | Power for autopilot  | 5V                       |
| 2                  | UART6_TX    | Output (5V) TX -> RX | 3                        |
| 3                  | UART6_RX    | Input (5V) RX -> TX  | 2                        |
| 4                  | APQ_GPIO_29 | (5V)                 | Not connected            |
| 5                  | GND         |                      | 6                        |
| 6                  | APQ_GPIO_30 | (5V)                 | Not connected            |

### GPS 接线

尽管3DR GPS要求5V输入，采用3.3V输入似乎也能很好地工作。 （内置的调节器MIC5205具有2.5V的最小工作电压）。

| Snapdragon J9 Pin | Signal   | Comment       | 3DR GPS 6pin/4pin | Pixfalcon GPS pin |
| ----------------- | -------- | ------------- | ----------------- | ----------------- |
| 1                 | 3.3V     | (3.3V)        | 1                 | 4                 |
| 2                 | UART2_TX | Output (3.3V) | 2/-               | 3                 |
| 3                 | UART2_RX | Input (3.3V)  | 3/-               | 2                 |
| 4                 | I2C2_SDA | (3.3V)        | -/3               | 5                 |
| 5                 | GND      |               | 6/-               | 1                 |
| 6                 | I2C2_SCL | (3.3V)        | -/2               | 6                 |

##尺寸

 ![硬件-晓龙-尺寸](../pictures/hardware\hardware-snapdragon-dimensions.png)



