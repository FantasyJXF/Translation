# Software Update

官网英文原文地址：http://px4.osdrone.net/13_Software_Update/13_software_update_md.html

The method to update the PX4 software on the drone depends on the hardware platform. For microcontroller based applications new Firmware is flashed through USB or serial.

## Infrastructure




## Flashing the Bootloader



```arm-none-eabi-gdb
  (gdb) tar ext /dev/serial/by-id/usb-Black_Sphere_XXX-if00
  (gdb) mon swdp_scan
  (gdb) attach 1
  (gdb) load tapv1_bl.elf
        ...
        Transfer rate: 17 KB/sec, 828 bytes/write.
  (gdb) kill```

