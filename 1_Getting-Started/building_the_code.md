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

现在可以由源代码编译二进制代码了。但在直接使用硬件前，推荐先[进行仿真](../4_Simulation/basic_simulation.md)。喜欢在图形界面开发环境工作的用户也应该继续完成下面部分。


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

A successful run will end with this output:

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

```sh
scp build_posix_rpi2_release/src/firmware/posix/mainapp pi@YOUR_PI:/home/pi/
```

And run it with :
<div class="host-code"></div>

```sh
./mainapp
```

If you're building *directly* on the Pi, you will want the native build target (posix_pi2_default).

<div class="host-code"></div>

```sh
cd Firmware
make posix_rpi2_default # for native build
```

The "mainapp" executable file is in the directory build_posix_rpi2_default/src/firmware/posix.
Run it directly with :
<div class="host-code"></div>

```sh
./build_posix_rpi2_default/src/firmware/posix/mainapp
```

A successful build followed by executing mainapp will give you this :

```sh
[init] shell id: 1996021760
[init] task name: mainapp

______  __   __    ___
| ___ \ \ \ / /   /   |
| |_/ /  \ V /   / /| |
|  __/   /   \  / /_| |
| |     / /^\ \ \___  |
\_|     \/   \/     |_/

Ready to fly.


pxh>
```

### QuRT / Snapdragon based boards

#### Build it

The commands below build the targets for the Linux and the DSP side. Both executables communicate via [muORB](../6_Middleware-and-Architecture/uorb_messaging.md).

<div class="host-code"></div>

```sh
cd Firmware
make eagle_default
```

To load the SW on the device, connect via USB cable and make sure the device is booted. Run this in a new terminal window:

<div class="host-code"></div>

```sh
adb shell
```

Go back to previous terminal and upload:

<div class="host-code"></div>

```sh
make eagle_default upload
```


>Note that this will also copy (and overwrite) the two config files [mainapp.config](https://github.com/PX4/Firmware/blob/master/posix-configs/eagle/flight/mainapp.config) and [px4.config](https://github.com/PX4/Firmware/blob/master/posix-configs/eagle/flight/px4.config) to the device. Those files are stored under /usr/share/data/adsp/px4.config and /home/linaro/mainapp.config respectively if you want to edit the startup scripts directly on your vehicle.


The mixer currently needs to be copied manually:

<div class="host-code"></div>

```
adb push ROMFS/px4fmu_common/mixers/quad_x.main.mix  /usr/share/data/adsp
```

#### Run it

Run the DSP debug monitor:

<div class="host-code"></div>

```sh
${HEXAGON_SDK_ROOT}/tools/mini-dm/Linux_Debug/mini-dm
```

Go back to ADB shell and run mainapp:

```sh
cd /home/linaro
./mainapp mainapp.config
```

Note that the mainapp will stop as soon as you disconnect the USB cable (or if you ssh session is disconnected). To fly, you should make the mainapp auto-start after boot.

#### Auto-start mainapp

To run the mainapp as soon as the Snapdragon has booted, you can add the startup to `rc.local`:

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

```
(cd /home/linaro && ./mainapp mainapp.config > mainapp.log)

exit 0
```

Make sure that the `rc.local` is executable:

```
adb shell
chmod +x /etc/rc.local
```

Then reboot the Snapdragon:

```
adb reboot
```

## Compiling in a graphical IDE

The PX4 system supports Qt Creator, Eclipse and Sublime Text. Qt Creator is the most user-friendly variant and hence the only officially supported IDE. Unless an expert in Eclipse or Sublime, their use is discouraged. Hardcore users can find an [Eclipse project](https://github.com/PX4/Firmware/blob/master/.project) and a [Sublime project](https://github.com/PX4/Firmware/blob/master/Firmware.sublime-project) in the source tree.

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

## Qt Creator Functionality

Qt creator offers clickable symbols, auto-completion of the complete codebase and building and flashing firmware.

![qtcreator](../pictures/toolchain/qtcreator.png)

### Qt Creator on Linux

Before starting Qt Creator, the [project file](https://cmake.org/Wiki/CMake_Generator_Specific_Information#Code::Blocks_Generator) needs to be created:

<div class="host-code"></div>

```sh
cd ~/src/Firmware
mkdir ../Firmware-build
cd ../Firmware-build
cmake ../Firmware -G "CodeBlocks - Unix Makefiles"
```

Then load the CMakeLists.txt in the root firmware folder via File -> Open File or Project -> Select the CMakeLists.txt file.

After loading, the 'play' button can be configured to run the project by selecting 'custom executable' in the run target configuration and entering 'make' as executable and 'upload' as argument.

### Qt Creator on Windows

<aside class="todo">
Windows has not been tested with Qt creator yet.
</aside>

### Qt Creator on Mac OS

Before starting Qt Creator, the [project file](https://cmake.org/Wiki/CMake_Generator_Specific_Information#Code::Blocks_Generator) needs to be created:

<div class="host-code"></div>

```sh
cd ~/src/Firmware
mkdir build_creator
cd build_creator
cmake .. -G "CodeBlocks - Unix Makefiles"
```

That's it! Start Qt Creator, then complete the steps in the video below to set up the project to build.

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

