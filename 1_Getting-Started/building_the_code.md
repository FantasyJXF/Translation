# 编译px４软件
官网英文原文地址：http://dev.px4.io/starting-building.html

PX4可以在控制台或者图形界面/IDE开发

## 在控制台编译
在去到图形界面或者IDE前，验证系统设置的正确性非常重要，因此打开控制台。在 OS X, 敲击 ⌘-space ，并搜索'terminal'。在Ubuntu，单击启动栏，搜索“terminal”（或者trl+alt+T）。在windows平台，在开始菜菜单找到px4文件夹，单击'PX4 Console'

![building](../pictures/toolchain/terminal.png)

终端在Home目录启动，我们默认去到'~/src/Firmware' 然后，克隆顶层资源库。有经验的开发者可以克隆自己的复制的[资源库](https://help.github.com/articles/fork-a-repo/) 

<div class="host-code"></div>

```sh
mkdir -p ~/src
cd ~/src
git clone https://github.com/PX4/Firmware.git
cd Firmware
git submodule update --init --recursive
cd ..
```

上述命令结束以后，还**不能**直接对源代码进行编译，直接编译可能会出现内存溢出的错误`error:ld returned 1 exit status`，原因是arm-none-eabi 4.7.4版本不对，直接编译失败的结果显示如下。

<div class="host-code"></div>

```sh
collect2.exe:error:ld returned 1 exit status
make[3]: *** [src/firmware/nuttx/firmware_muttx] Error 1
make[2]: *** [src/firmware/nuttx/CMakeFiles/firmware_muttx.dir/all] Error 2
make[1]: *** [all] Error 2
make: *** [px4fmu-v2_default] Error 2
```
这个问题可以通过下载[4.8.4版本](http://pan.baidu.com/s/1c1QzUU0)，进行解压后复制并替换掉PX4 Toolchain安装目录下Toolchain文件夹内的相应文件即可。
在直接使用硬件前，推荐先[进行仿真](../4_Simulation/basic_simulation.md)。喜欢在图形界面开发环境工作的用户也应该继续完成下面部分。


###基于NuttX / Pixhawk的硬件板

<div class="host-code"></div>

```sh
cd Firmware
make px4fmu-v2_default
```

注意到“make”是一个字符命令编译工具，“px4fmu-v2”是硬件/ardupilot版本，“default”是默认配置，所有的PX4编译目标遵循这个规则。 

成功编译的最后输出是这样的：

<div class="host-code"></div>

```sh
[100%] Linking CXX executable firmware_nuttx
[100%] Built target firmware_nuttx
Scanning dependencies of target build_firmware_px4fmu-v2
[100%] Generating nuttx-px4fmu-v2-default.px4
[100%] Built target build_firmware_px4fmu-v2
```

通过在命令后面添加‘upload’，编译的二进制程序就会通过USB上传到飞控硬件:

<div class="host-code"></div>

```sh
make px4fmu-v2_default upload
```

上传成功时输出情况如下：

<div class="host-code"></div>

```sh
Erase  : [====================] 100.0%
Program: [====================] 100.0%
Verify : [====================] 100.0%
Rebooting.

[100%] Built target upload
```

### Raspberry Pi 2 boards

The command below builds the target for Raspbian (posix_pi2_release).

<div class="host-code"></div>

```sh
cd Firmware
make posix_rpi2_release # for cross-compiler build
```

The "mainapp" executable file is in the directory build_posix_rpi2_release/src/firmware/posix.
Copy it over to the RPi (replace YOUR_PI with the IP or hostname of your RPi, [instructions how to access your RPi](../5_Autopilot-Hardware/raspeberry_pi2.md#developer-quick-start))

Then set the IP (or hostname) of your RPi using:

```sh
export AUTOPILOT_HOST=192.168.X.X
```

And upload it with:

```sh
cd Firmware
make posix_rpi_cross upload # for cross-compiler build
```

Then, connect over ssh and run it with (as root):

```sh
sudo ./px4 px4.config
```

#### Native build

If you're building *directly* on the Pi, you will want the native build target (posix_rpi_native).

```sh
cd Firmware
make posix_rpi_native # for native build
```

The "px4" executable file is in the directory build_posix_rpi_native/src/firmware/posix.
Run it directly with:

```sh
sudo ./build_posix_rpi_native/src/firmware/posix/px4 ./posix-configs/rpi/px4.config
```

A successful build followed by executing px4 will give you something like this:

```sh

______  __   __    ___
| ___ \ \ \ / /   /   |
| |_/ /  \ V /   / /| |
|  __/   /   \  / /_| |
| |     / /^\ \ \___  |
\_|     \/   \/     |_/

px4 starting.


pxh>
```

#### Autostart
To autostart px4, add the following to the file `/etc/rc.local` (adjust it
accordingly if you use native build), right before the `exit 0` line:
```
cd /home/pi && ./px4 -d px4.config > px4.log
```


### Parrot Bebop

Support for the Bebop is really early stage and should be used very carefully.

#### Build it
```sh
cd Firmware
make posix_bebop_default
```

Turn on your Bebop and connect your host machine with the Bebop's wifi. Then, press the power button
four times to enable ADB and to start the telnet daemon.

```sh
make posix_bebop_default upload
```

This will upload the PX4 mainapp into /usr/bin and create the file /home/root/parameters if not already
present. In addition, we need the Bebop's mixer file and the px4.config. Currently, both files have
to be copied manually using the following commands.
```sh
adb connect 192.168.42.1:9050
adb push ROMFS/px4fmu_common/mixers/bebop.main.mix /home/root
adb push posix-configs/bebop/px4.config /home/root
adb disconnect
```

#### Run it
Connect to the Bebop's wifi and press the power button four times. Next,
connect with the Bebop via telnet or adb shell and run the commands bellow.

```sh
telnet 192.168.42.1
```

Kill the Bebop's proprietary driver with
```sh
kk
```
and start the PX4 mainapp with:
```sh
px4 /home/root/px4.config
```

In order to fly the Bebop, connect a joystick device with your host machine and start QGroundControl. Both,
the Bebop and the joystick should be recognized. Follow the instructions to calibrate the sensors
and setup your joystick device.

#### Autostart

To auto-start PX4 on the Bebop at boot, modify the init script `/etc/init.d/rcS_mode_default`. Comment the following line:
```
DragonStarter.sh -out2null &
```
Replace it with:
```
px4 -d /home/root/px4.config > /home/root/px4.log
```

Enable adb server by pressing the power button 4 times and connect to adb server as described before:
```sh
adb connect 192.168.42.1:9050
```
Re-mount the system partition as writeable:
```sh
adb shell mount -o remount,rw /
```
In order to avoid editing the file manually, you can use this one : https://gist.github.com/mhkabir/b0433f0651f006e3c7ac4e1cbd83f1e8

Save the original one and push this one to the Bebop
```sh
adb shell cp /etc/init.d/rcS_mode_default /etc/init.d/rcS_mode_default_backup
adb push rcS_mode_default /etc/init.d/
```
Sync and reboot:
```sh
adb shell sync
adb shell reboot
```

### QuRT / Snapdragon based boards

#### Build it

The commands below build the targets for the Linux and the DSP side. Both executables communicate via [muORB](advanced-uorb.md).

```sh
cd Firmware
make eagle_default
```

To load the SW on the device, connect via USB cable and make sure the device is booted. Run this in a new terminal window:

```sh
adb shell
```

Go back to previous terminal and upload:

```sh
make eagle_default upload
```

Note that this will also copy (and overwrite) the two config files [mainapp.config](https://github.com/PX4/Firmware/blob/master/posix-configs/eagle/flight/mainapp.config) and [px4.config](https://github.com/PX4/Firmware/blob/master/posix-configs/eagle/flight/px4.config) to the device. Those files are stored under /usr/share/data/adsp/px4.config and /home/linaro/mainapp.config respectively if you want to edit the startup scripts directly on your vehicle.

The mixer currently needs to be copied manually:

```sh
adb push ROMFS/px4fmu_common/mixers/quad_x.main.mix  /usr/share/data/adsp
```

#### Run it

Run the DSP debug monitor:

```sh
${HEXAGON_SDK_ROOT}/tools/debug/mini-dm/Linux_Debug/mini-dm
```

Note: alternatively, especially on Mac, you can also use [nano-dm](https://github.com/kevinmehall/nano-dm).

Go back to ADB shell and run px4:

```sh
cd /home/linaro
./px4 mainapp.config
```

Note that the px4 will stop as soon as you disconnect the USB cable (or if you ssh session is disconnected). To fly, you should make the px4 auto-start after boot.

#### Autostart

To run the px4 as soon as the Snapdragon has booted, you can add the startup to `rc.local`:

Either edit the file `/etc/rc.local` directly on the Snapdragon:

```sh
adb shell
vim /etc/rc.local
```

Or copy the file to your computer, edit it locally, and copy it back:

```sh
adb pull /etc/rc.local
gedit rc.local
adb push rc.local /etc/rc.local
```

For the auto-start, add the following line before `exit 0`:

```sh
(cd /home/linaro && ./px4 mainapp.config > mainapp.log)

exit 0
```

Make sure that the `rc.local` is executable:

```sh
adb shell
chmod +x /etc/rc.local
```

Then reboot the Snapdragon:

```sh
adb reboot
```


##图形IDE界面下编译
PX4 支持Qt Creator, Eclipse 和Sublime Text三种集成式开发环境。  Qt Creator是最友好的开发环境，所以被是唯一官方支持的IDE。除非资深的Eclipse 或Sublime开发者，否则一般不推荐使用Eclipse或Sublime进行二次开发。硬件底层开发可以在 [Eclipse project](https://github.com/PX4/Firmware/blob/master/.project) 和 a [Sublime project](https://github.com/PX4/Firmware/blob/master/Firmware.sublime-project) 找到源码。

{% raw %}
<video id="my-video" class="video-js" controls preload="auto" width="100%" 
poster="../pictures/diagrams/qtcreator.PNG" data-setup='{"aspectRatio":"16:9"}'>
  <source src="http://7xvob5.com1.z0.glb.clouddn.com/PX4%20Flight%20Stack%20Build%20Experience.mp4" type='video/mp4' >
  <p class="vjs-no-js">
    To view this video please enable JavaScript, and consider upgrading to a web browser that
    <a href="http://videojs.com/html5-video-support/" target="_blank">supports HTML5 video</a>
  </p>
</video>
{% endraw %}

## Qt Creator 功能
Qt creator 提供单击选择变量、代码自动补全、代码编译和固件上传等功能。

![qtcreator](../pictures/toolchain/qtcreator.png)

### Linux 平台的 Qt Creator 

在启动Qt creator之前,  需要先创建[工程文件](https://cmake.org/Wiki/CMake_Generator_Specific_Information#Code::Blocks_Generator) :

<div class="host-code"></div>

```sh
cd ~/src/Firmware
mkdir ../Firmware-build
cd ../Firmware-build
cmake ../Firmware -G "CodeBlocks - Unix Makefiles" -DCONFIG=nuttx_px4fmu-v2_default
```
接着启动Qt creator（如果系统没安装Qt Creator 百度一下linux下安装Qt Creator，然后再启动Qt Creator）并加载 Firmware 根目录下 CMakeLists.txt 文件，步骤：点击工具栏 File -> Open File or Project -> Select the CMakeLists.txt file 。
如果加载提示ninja没有安装，请按照“高级Linux”章节进行ninja编译工具的安装，安装完成后，log out（登出）并log in（登入）。

加载了文件后，点击左侧projects按钮，在run onfiguration栏选择'custom executable',在executable 栏里输入'make'， argument栏输入 'upload'，将‘play’按钮配置成运行工程。


### Windows平台的 Qt Creator 

<aside class="todo">
Windows平台下的Qt Creator开发目前也没经过详细测试。
</aside>

### Mac OS 平台的  Qt Creator 

启动 Qt Creator 之前，需要先创建 [project file](https://cmake.org/Wiki/CMake_Generator_Specific_Information#Code::Blocks_Generator) ：

<div class="host-code"></div>

```sh
cd ~/src/Firmware
mkdir build_creator
cd build_creator
cmake .. -G "CodeBlocks - Unix Makefiles"
```

完成上述步骤以后，启动 Qt Creator, 完成下面视频中的步骤，就可以进行工程文件的编译了。

{% raw %}
<video id="my-video" class="video-js" controls preload="auto" width="100%" 
poster="../pictures/diagrams/macQt.PNG" data-setup='{"aspectRatio":"16:9"}'>
  <source src="http://7xvob5.com2.z0.glb.qiniucdn.com/PX4%20Cmake%20Project%20Setup%20on%20Mac%20OS.mp4" type='video/mp4' >
  <p class="vjs-no-js">
    To view this video please enable JavaScript, and consider upgrading to a web browser that
    <a href="http://videojs.com/html5-video-support/" target="_blank">supports HTML5 video</a>
  </p>
</video>
{% endraw %}

