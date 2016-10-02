# Crazyflie 2.0

官网英文原文地址：http://dev.px4.io/hardware-crazyflie2.html

Crazyfile系列的微型四轴飞行器是由Bitcraze AB创建的。关于Crazyfile 2(CF2)的概况在[这里](https://www.bitcraze.io/crazyflie-2/)。

![crazy](/pictures/hardware/crazyflie2.png)

## 简要概括

主要的硬件文档在[这里](https://wiki.bitcraze.io/projects:crazyflie2:index):
* 系统主芯片：STM32F405RG
  * CPU: 带单精度FPU的168 MHz ARM Cortex M4
  * RAM: 192KB SRAM
* nRF51822电台和电源管理控制器
* MPU9250加速度计/陀螺仪/磁力计
* LPS25H气压计


## 刷固件

设置好PX4开发环境之后，按照以下步骤可将PX4软件安装到CF2上：

1. 从GitHub上获取PX4 [Bootloader](https://github.com/PX4/Bootloader)的源码

2. 用`make crazyflie_bl`指令进行编译

3. 让CF2进入DFU模式
      - 保证其开始时未上电
      - 按住按键
      - 连接到电脑的USB口
      - 一秒后，蓝色的LED灯应该开始闪烁，五秒后应该闪的更快
      - 松开按键

4. 使用dfu-util刷Bootloader，输入指令

  ```sudo dfu-util -d 0483:df11 -a 0 -s 0x08000000 -D crazyflie_bl.bin```

  完成后断开CF2。
      - 如果成功的话，CF2再次连接时黄色的LED灯应该闪烁

5. Grab the [Firmware](https://github.com/PX4/Firmware)

6. Compile with `make crazyflie_default upload`

7. When prompted to plug in device, plug in CF2: the yellow LED should start blinking indicating bootloader mode. Then the red LED should turn on indicating that the flashing process has started.

8. Wait for completion

9. Done! Calibrate via QGC

## Wireless


The onboard nRF module allows connecting to the board via Bluetooth or through the proprietary 2.4GHz Nordic ESB protocol.
- A [Crazyradio PA](https://www.bitcraze.io/crazyradio-pa/) is recommended.
- To fly the CF2 right away, the Crazyflie phone app is supported via Bluetooth

Using the official Bitcraze **Crazyflie phone app**
- Connect via Bluetooth
- Change mode in settings to 1 or 2
- Calibrate via QGC

Connecting via **MAVLink**
- Use a Crazyradio PA alongside a compatible GCS
- See [cfbridge](https://github.com/dennisss/cfbridge) for how to connect any UDP capable GCS to the radio

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


