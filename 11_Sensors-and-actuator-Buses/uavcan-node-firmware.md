# UAVCAN 固件升级

官网英文原文地址：http://dev.px4.io/uavcan-node-firmware.html

## Vectorcontrol矢量控制电子调速器 ESC 代码库 (Pixhawk ESC 1.6 and S2740VC)

Download the ESC code下载ESC代码:

<div class="host-code"></div>

```sh
git clone https://github.com/thiemar/vectorcontrol
cd vectorcontrol
```

### Flashing 刷新UAVCAN启动程序the UAVCAN Bootloader

PIxhawk ESC 1.6在通过UAVCAN设备更新固件之前, 要求UAVCAN首先刷新启动程序。为了生成启动程序，运行Before updating firmware via UAVCAN, the Pixhawk ESC 1.6 requires the UAVCAN bootloader be flashed. To build the bootloader, run:

<div class="host-code"></div>

```sh
make clean && BOARD=px4esc_1_6 make -j8
```

After building, the bootloader image is located at启动程序生成之后，其image文件存放路径为 `firmware/px4esc_1_6-bootloader.bin`, and the OpenOCD configuration is located at OpenOCD的配置文档为 `openocd_px4esc_1_6.cfg`. Follow可以通过 [如下教程these instructions](../11_Sensors-and-actuator-Buses/uavcan-node-enumeration.md) 初始化ESC的启动程序。to install the bootloader on the ESC.

### 编译主要的二进制（.bin）文件Compiling the Main Binary

<div class="host-code"></div>

```sh
BOARD=s2740vc_1_0 make && BOARD=px4esc_1_6 make
```

这将会生成两个UAVCAN的固件，它们都支持ESCs。This will build the UAVCAN node firmware for both supported ESCs.  它们固件image文件存放路径为 The firmware images will be located at `com.thiemar.s2740vc-v1-1.0-1.0.<git hash>.bin` 和`org.pixhawk.px4esc-v1-1.6-1.0.<git hash>.binn`.

## Sapog 代码库Codebase (Pixhawk ESC 1.4)

Download the Sapog codebase 下载Sapog代码库:

<div class="host-code"></div>

```sh
git clone https://github.com/PX4/sapog
cd sapog
git submodule update --init --recursive
```

### 刷新UAVCAN启动程序 Flashing the UAVCAN Bootloader

Pixhawk ESC 1.4 在通过UAVCAN刷新固件之前，需要UAVCAN已经刷新启动程序。启动程序的编译生成方法如下 Before updating firmware via UAVCAN, the Pixhawk ESC 1.4 requires the UAVCAN bootloader be flashed. The bootloader can be built as follows:

<div class="host-code"></div>

```sh
cd bootloader
make clean && make -j8
cd ..
```

The bootloader image is located at 启动程序的image文件存放路径为 `bootloader/firmware/bootloader.bin`, and the OpenOCD configuration is located at  OpenOCD的配置文档为`openocd.cfg`. Follow 可以通过 [如下教程these instructions](../11_Sensors-and-actuator-Buses/uavcan-bootloader-installation.md) 初始化ESC的起始程序。to install the bootloader on the ESC.

### 编译主要的二进制（.bin）文件 Compiling the Main Binary

<div class="host-code"></div>

```sh
cd firmware
make sapog.image
```

 固件image文件存放路径为The firmware image will be located at `firmware/build/org.pixhawk.sapog-v1-1.0.<xxxxxxxx>.bin`, where此处 `<xxxxxxxx>` 是由任意数字和字母组成的序列。 is an arbitrary sequence of numbers and letters.

## Zubax GNSS

Please refer to the请参考 [项目网页 project page](https://github.com/Zubax/zubax_gnss) 去学习如何生成和刷新固件。to learn how to build and flash the firmware.

Zubax GNSS comes with 出厂时就带有支持UAVCAN的启动程序，因此其固件可以通过UAVCAN的统一方式进行更新，具体更新方式如下所述。a UAVCAN-capable bootloader, so its firmware can be updated in a uniform fashion via UAVCAN as described below.

## Autopilot固件初始化 Firmware Installation on the Autopilot

UAVCAN节点的文档命名遵循约定的命名方式，这种命名方式允许Pixhawk更新网络内所有的UAVCAN设备，无需考虑是哪个制造商生产的。The UAVCAN node file names follow a naming convention which allows the Pixhawk to update all UAVCAN devices on the network, regardless of manufacturer. 上述步骤产生的固件文件必须通过要复制到SD卡或PX4 ROMFS的正确的位置，以确保设备能够很好的更新。The firmware files generated in the steps above must therefore be copied to the correct locations on an SD card or the PX4 ROMFS in order for the devices to be updated.

固件image名称通常是The convention for firmware image names is:

  ```<uavcan name>-<hw version major>.<hw version minor>-<sw version major>.<sw version minor>.<version hash>.bin```

  e.g. ```com.thiemar.s2740vc-v1-1.0-1.0.68e34de6.bin```

然而，由于空间和性能的限制（命名不能够超过28个字符），UAVCAN固件升级需要将这些文件名分割存储在下面的目录结构里： However, due to space/performance constraints (names may not exceed 28 charates), the UAVCAN firmware updater requires those filenames to be split and stored in a directory structure like the following:

  ```/fs/microsd/fw/<node name>/<hw version major>.<hw version minor>/<hw name>-<sw version major>.<sw version minor>.<git hash>.bin```

 e.g. ```s2740vc-v1-1.0.68e34de6.bin```

基于ROMFS的更新遵循以下的模型，The ROMFS-based updater follows that pattern, but prepends the file name with ```_``` so you add the firmware in:

  ```/etc/uavcan/fw/<device name>/<hw version major>.<hw version minor>/_<hw name>-<sw version major>.<sw version minor>.<git hash>.bin```

## 将二进制文件放入PX4 ROMFS Placing the binaries in the PX4 ROMFS

最终生成的文件的位置为The resulting finale file locations are:

- S2740VC ESC: `ROMFS/px4fmu_common/uavcan/fw/com.thiemar.s2740vc-v1/1.0/_s2740vc-v1-1.0.<git hash>.bin`
- Pixhawk ESC 1.6: `ROMFS/px4fmu_common/uavcan/fw/org.pixhawk.px4esc-v1/1.6/_px4esc-v1-1.6.<git hash>.bin`
  - Pixhawk ESC 1.4: `ROMFS/px4fmu_common/uavcan/fw/org.pixhawk.sapog-v1/1.4/_sapog-v1-1.4.<git hash>.bin``
  - Zubax GNSS v1: `ROMFS/px4fmu_common/uavcan/fw/com.zubax.gnss/1.0/gnss-1.0.<git has>.bin`
  - Zubax GNSS v2: `ROMFS/px4fmu_common/uavcan/fw/com.zubax.gnss/2.0/gnss-2.0.<git has>.bin`

注意ROMFS/px4fmu_common目录将会挂载在Pixhawk的/etc目录下Note that the ROMFS/px4fmu_common directory will be mounted to /etc on Pixhawk.

### 开始固件升级过程Starting the Firmware Upgrade process

<aside class="note">
When using the当使用的是 [PX4飞行控制栈 Flight Stack](../2_Concepts/flight_stack.md)时, 在‘电源配置’部分中启用UAVCAN，并在尝试升级UAVCAN固件之前要重启系统。enable UAVCAN in the 'Power Config' section and reboot the system before attempting an UAVCAN firmware upgrade.
</aside>

或者可以通过以下方式在NSH上手动启动UAVCAN固件升级进程：Alternatively UAVCAN firmware upgrading can be started manually on NSH via:

```sh
uavcan start
uavcan start fw
```
