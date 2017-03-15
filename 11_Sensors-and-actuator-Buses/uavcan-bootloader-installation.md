# 无人机 CAN总线 引导程序UAVCAN Bootloader

官网英文原文地址：http://dev.px4.io/uavcan-bootloader-installation.html

# UAVCAN Bootloader Installation UAVCAN 引导程序的初始化

<aside class="warning">
无人机控制器局域网络（Unmanned Aerial Vehicle Controller Area Network，UAVCAN）UAVCAN devices typically ship with a bootloader pre-installed. 如果你不对UAVCAN设备进行开发，请不要试图去重复本章的任何操作。Do not follow the instructions in this section unless you are developing UAVCAN devices.
</aside>

## Overview

对于STM32设备，PX4项目包含一个标准的UAVCAN引导程序The PX4 project includes a standard UAVCAN bootloader for STM32 devices.

引导程序占用了flash内存的最开始8-16KB的位置，它们是设备上电后首先运行的代码。The bootloader occupies the first 8–16 KB of flash, and is the first code executed on power-up.通常，引导程序执行对设备的简单初始化，如：自动确定CAN总线的波特率， 担当UAVCAN动态ID节点客户端去获得唯一的 ID节点，Typically, the bootloader performs low-level device initialization, automatically determines the CAN bus baud rate, acts as a UAVCAN dynamic node ID client to obtain a unique node ID, and waits for confirmation from the flight controller before proceeding with application boot在运行应用初始化之前要等待飞行控制器去确认.

这个引导程序能确保UAVCAN设备在无效或者错误固件时无需人为干扰，就可以自动恢复。同时允许固件自动升级。This process ensures that a UAVCAN device can recover from invalid or corrupted application firmware without user intervention, and also permits automatic firmware updates.

## 前提条件Prerequisites

初始化或更新UAVCAN引导程序的前提条件Installing or updating the UAVCAN bootloader requires:

- An SWD or JTAG interface (depending on device), 一个SWD或者JTAG接口（取决于设备），比如说 [BlackMagic Probe](http://www.blacksphere.co.nz/main/blackmagic) 或 [ST-Link v2](http://www.st.com/internet/evalboard/product/251168.jsp);
- 一条连接你SWD或ＪＴＡＧ接口与ＵＡＶＣＡＮ设备调试端口的适配线。An adapter cable to connect your SWD or JTAG interface to the UAVCAN device's debugging port;
- A [supported ARM toolchain](../11_Sensors-and-actuator-Buses/uavcan-node-enumeration.md).

## 设备的qiaDevice Preparation
If you are unable to connect to your device using the instructions below, it's possible that firmware already on the device has disabled the MCU's debug pins. To recover from this, you will need to connect your interface's NRST or nSRST pin (pin 15 on the standard ARM 20-pin connector) to your MCU's NRST pin. Obtain your device schematics and PCB layout or contact the manufacturer for details.

## Installation

After compiling or obtaining a bootloader image for your device (refer to device documentation for details), the bootloader must be copied to the beginning of the device's flash memory.

The process for doing this depends on the SWD or JTAG interface used.

## BlackMagic Probe

Ensure your BlackMagic Probe [firmware is up to date](https://github.com/blacksphere/blackmagic/wiki/Hacking).

Connect the probe to your UAVCAN device, and connect the probe to your computer.

Identify the probe's device name. This will typically be `/dev/ttyACM<x>` or `/dev/ttyUSB<x>`.

Power up your UAVCAN device, and run:

<div class="host-code"></div>

```sh
arm-none-eabi-gdb /path/to/your/bootloader/image.elf
```

At the `gdb` prompt, run:

<div class="host-code"></div>

```gdb
target extended /dev/ttyACM0
monitor connect_srst enable
monitor swdp_scan
attach 1
set mem inaccessible-by-default off
load
run
```

If `monitor swdp_scan` returns an error, ensure your wiring is correct, and that you have an up-to-date version of the BlackMagic firmware.

## ST-Link v2

Ensure you have a recent version—at least 0.9.0—of [OpenOCD](http://openocd.org).

Connect the ST-Link to your UAVCAN device, and connect the ST-Link to your computer.

Power up your UAVCAN device, and run:

<div class="host-code"></div>

```sh
openocd -f /path/to/your/openocd.cfg &
arm-none-eabi-gdb /path/to/your/bootloader/image.elf
```

At the `gdb` prompt, run:

<div class="host-code"></div>

```gdb
target extended-remote localhost:3333
monitor reset halt
set mem inaccessible-by-default off
load
run
```

## Segger J-Link Debugger

Connect the JLink Debugger to your UAVCAN device, and connect the JLink Debugger to your computer.

Power up your UAVCAN device, and run:

<div class="host-code"></div>

```JLinkGDBServer -select USB=0 -device STM32F446RE -if SWD-DP -speed 20000 -vd```

Open a second terminal, navigate to the directory that includes the px4esc_1_6-bootloader.elf for the esc and run:

<div class="host-code"></div>

```arm-none-eabi-gdb px4esc_1_6-bootloader.elf```

At the `gdb` prompt, run:

<div class="host-code"></div>

```tar ext :2331
load
```

## Erasing Flash with SEGGER JLink Debugger

As a recovery method it may be useful to erase flash to factory defaults such that the firmware is using the default parameters. Go to the directory of your SEGGER installation and launch JLinkExe, then run:

```
device <name-of-device>
erase
```

Replace `<name-of-device>` with the name of the microcontroller, e.g. STM32F446RE for the Pixhawk ESC 1.6 or STM32F302K8 for the SV2470VC ESC.
