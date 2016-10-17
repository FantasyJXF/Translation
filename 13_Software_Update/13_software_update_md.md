# 更新软件

官网英文原文地址：http://dev.px4.io/software_update.html

The method to update the PX4 software on the drone depends on the hardware platform. For microcontroller based applications new Firmware is flashed through USB or serial.
更新无人机上面的PX4软件的方法依据硬件不同而不同。对于基于微控制器，通过USB或者是串口来烧写固件。

## 下层构造（Infrastructure）




## 烧写 Bootloader



```arm-none-eabi-gdb
  (gdb) tar ext /dev/serial/by-id/usb-Black_Sphere_XXX-if00
  (gdb) mon swdp_scan
  (gdb) attach 1
  (gdb) load tapv1_bl.elf
        ...
        Transfer rate: 17 KB/sec, 828 bytes/write.
  (gdb) kill```
