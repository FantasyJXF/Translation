# Crazyflie 2.0

官网英文原文地址：http://dev.px4.io/hardware-crazyflie2.html

The Crazyflie line of micro quads was created by Bitcraze AB. An overview of the Crazyflie 2 (CF2) is here: https://www.bitcraze.io/crazyflie-2/

![crazy](/pictures/hardware/crazyflie2.png)

## Quick Summary

<aside class="tip">
The main hardware documentation is here: https://wiki.bitcraze.io/projects:crazyflie2:index
</aside>

  * Main System-on-Chip: STM32F405RG
    * CPU: 168 MHz ARM Cortex M4 with single-precision FPU
    * RAM: 192 KB SRAM
  * nRF51822 radio and power management MCU
  * MPU9250 Accel / Gyro / Mag
  * LPS25H barometer


## Flashing

After setting up the PX4 development environment, follow these steps to put the PX4 software on the CF2:

1. Grab source code of the PX4 [Bootloader](https://github.com/PX4/Bootloader)

2. Compile using `make crazyflie_bl`

3. Put the CF2 into DFU mode:
	- Ensure it is initially unpowered
	- Hold down button
	- Plug into computer's USB port
	- After a second, the blue LED should start blinking and after 5 seconds should start blinking faster
	- Release button

4. Flash bootloader using dfu-util: `sudo dfu-util -d 0483:df11 -a 0 -s 0x08000000 -D crazyflie_bl.bin` and unplug CF2 when done
	- If successful, then the yellow LED should blink when plugging in again

5. Grab the [Firmware](https://github.com/PX4/Bootloader)

6. Compile with `make crazyflie_default upload`

7. When prompted to plug in device, plug in CF2: the yellow LED should start blinking indicating bootloader mode. Then the red LED should turn on indicating that the flashing process has started.

8. Wait for completion

9. Done! Calibrate via QGC

## Wireless

NOTE: The drivers for wireless communication are still in development and not fully available.

The onboard nRF module allows connecting to the board via Bluetooth or through the proprietary 2.4GHz Nordic ESB protocol.
- A [Crazyradio PA](https://www.bitcraze.io/crazyradio-pa/) is recommended.
- To fly the CF2, currently only the Crazyflie phone app is supported via Bluetooth

## Flying

{% raw %}

<video id="my-video" class="video-js" controls preload="auto" width="100%"

poster="../pictures/hardware/crazy.png" data-setup='{"aspectRatio":"16:9"}'>

 <source src="http://7xvob5.com2.z0.glb.qiniucdn.com/Crazyflie%202.0-%20PX4%20Manual%20Stabilized.mp4" type='video/mp4' >

 <p class="vjs-no-js">

 To view this video please enable JavaScript, and consider upgrading to a web browser that

 <a href="http://videojs.com/html5-video-support/" target="_blank">supports HTML5 video</a>

 </p>

</video>

{% endraw %}


