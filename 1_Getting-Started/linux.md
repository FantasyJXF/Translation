# Linux开发环境
官网英文原文地址：http://dev.px4.io/starting-installing-linux.html

我们使用Debian / Ubuntu LTS 作为Linux的标准支持版本，但是也支持[Cent OS 和 Arch Linux的发行版本](../1_Getting-Started/adcanced_linux.md)

## 权限设置

<aside class="note">
注意：永远不要使用｀sudo｀来修复权限问题，否则会带来更多的权限问题，需要重装系统来解决。
</aside>

把用户添加到用户组　"dialout":

<div class="host-code"></div>

```sh
sudo usermod -a -G dialout $USER
```

然后注销后，重新登录，因为重新登录后所做的改变才会有效。

## 安装

更新包列表，安装下面编译PX4的依赖包。PX4主要支持的家族：

- NuttX based hardware: [Pixhawk](../5_Autopilot-Hardware/pixhawk.md), [Pixfalcon](../5_Autopilot-Hardware/pixfalcon.md)
- Snapdragon Flight hardware: [Snapdragon](../5_Autopilot-Hardware/snapgragon_flight.md)
- Raspberry Pi hardware: [Raspberry Pi 2](../5_Autopilot-Hardware/raspeberry_pi2.md)
- Host simulation: [jMAVSim SITL](../4_Simulation/basic_simulation.md) and [Gazebo SITL](../4_Simulation/gazebo_simulation.md)



> 注意：安装[Ninja Build System](https://fantasyjxf.gitbooks.io/px4-wiki/content/1_Getting-Started/adcanced_linux.html#Ninja构建系统)可以比make更快进行编译。如果安装了它就会自动选择使用它进行编译。




<div class="host-code"></div>

```sh
sudo add-apt-repository ppa:george-edison55/cmake-3.x -y
sudo apt-get update
sudo apt-get install python-argparse git-core wget zip \
    python-empy qtcreator cmake build-essential genromfs -y
# simulation tools
sudo apt-get install ant protobuf-compiler libeigen3-dev libopencv-dev openjdk-7-jdk openjdk-7-jre clang-3.5 lldb-3.5 -y
```

### NuttX的基本硬件

Ubuntu配备了一系列代理管理，这会严重干扰任何机器人相关的串口（或usb串口），卸载掉它也不会有什么影响:

<div class="host-code"></div>

```sh
sudo apt-get remove modemmanager
```

更新包列表和安装下面的依赖包。务必安装指定的版本的包

<div class="host-code"></div>

```sh
sudo add-apt-repository ppa:terry.guo/gcc-arm-embedded -y
sudo add-apt-repository ppa:team-gcc-arm-embedded/ppa
sudo apt-get update
sudo apt-get install python-serial openocd \
    flex bison libncurses5-dev autoconf texinfo build-essential \
    libftdi-dev libtool zlib1g-dev \
    python-empy gcc-arm-none-eabi -y
```

如果`gcc-arm-none-eabi`版本导致PX4/Firmware编译错误，请参考   [the bare metal installation instructions](../1_Getting-Started/adcanced_linux.md#toolchain-installation) 手动安装4.8版本。

### 骁龙

#### 工具链安装

首先添加Ubuntu官方平板团队的仓库，然后安装ADB和ARM交叉编译工具链。

<div class="host-code"></div>

```sh
sudo add-apt-repository ppa:phablet-team/tools && sudo apt-get update -y
```

<div class="host-code"></div>

```sh
sudo apt-get install gcc-arm-linux-gnueabihf g++-arm-linux-gnueabihf android-tools-adb android-tools-fastboot fakechroot fakeroot -y
```

安装向导将会启动，使用默认设置，一路回车即可。


骁龙的开发者应该在[这里](https://developer.qualcomm.com/software/hexagon-dsp-sdk/tool-request)申请Hexagon 7.2.10 Linux工具链和Hexagon 2.0 SDK for Linux。


下载好Hexagon SDK和Hexagon工具链后，clone这个仓库：

<div class="host-code"></div>

```sh
git clone https://github.com/ATLFlight/cross_toolchain.git
```

按照下面命令，把相关文件移动到cross_toolchain/downloads中：

<div class="host-code"></div>

```sh
mv qualcomm_hexagon_sdk_2_0_eval.bin cross_toolchain/downloads
mv Hexagon.LNX.7.2\ Installer-07210.1.tar cross_toolchain/downloads
cd cross_toolchain/downloads
tar -xf Hexagon.LNX.7.2\ Installer-07210.1.tar
```

执行下列命令安装SDK和工具链：

<div class="host-code"></div>

```sh
cd ../
./install.sh
```

按照说明配置开发环境。如果使用默认安装设置，那么可以在任何时候重新运行命令配置开发环境。此种情况将只安装缺少的组件。

交叉编译工具链和SDK会被安装到"$HOME/Qualcomm/..."，在"~/.bashrc"中添加：

<div class="host-code"></div>

```sh
export HEXAGON_SDK_ROOT="${HOME}/Qualcomm/Hexagon_SDK/2.0"
export HEXAGON_TOOLS_ROOT="${HOME}/Qualcomm/HEXAGON_Tools/7.2.10/Tools"
export HEXAGON_ARM_SYSROOT="${HOME}/Qualcomm/Hexagon_SDK/2.0/sysroot"
export PATH="${HEXAGON_SDK_ROOT}/gcc-linaro-arm-linux-gnueabihf-4.8-2013.08_linux/bin:$PATH"
```

载入新的配置：

<div class="host-code"></div>

```sh
source ~/.bashrc
```

#### 升级ADSP固件

在构建，烧写以及运行代码之前，还需要升级[ADSP固件](../12_Debugging-and-Advanced-Topics/advanced-snapdragon.md#updating-the-adsp-firmware)。

#### 参考

[GettingStarted](https://github.com/ATLFlight/ATLFlightDocs/blob/master/GettingStarted.md)是另外一个工具链安装向导。[HelloWorld](https://github.com/ATLFlight/HelloWorld)和[DSPAL tests](https://github.com/ATLFlight/dspal/tree/master/test/dspal_tester)可以用来验证工具链安装和DSP镜像。

DSP的信息可以通过mini-dm查看。

<div class="host-code"></div>

```sh
$HOME/Qualcomm/Hexagon_SDK/2.0/tools/mini-dm/Linux_Debug/mini-dm
```

### 树莓派

树莓派开发者应该从下面地址下载树莓派Linux工具链。安装脚本会自动安装交叉编译工具链。如果想要用原生树莓派工具链在树莓派上直接编译，参见[这里](../5_Autopilot-Hardware/raspeberry_pi2.md#native-builds-optional)。

<div class="host-code"></div>

```sh
git clone https://github.com/pixhawk/rpi_toolchain.git
cd rpi_toolchain
./install_cross.sh
```

在工具链安装过程中需要输入密码。

如果不想把工具链安装在默认位置```/opt/rpi_toolchain```，可以执行``` ./install_cross.sh <PATH>```向安装脚本传入其它地址。安装脚本会自动配置需要的环境变量。

## 完成

继续，进行[第一次构建](../1_Getting-Started/building_the_code.md)!
