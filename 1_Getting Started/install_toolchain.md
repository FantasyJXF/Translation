# 安装工具链

1.1初始配置
在开始开发PX4之前，系统需要按照默认配置进行初始配置，以保证硬件设置合适，被检测到。下面的一个视频介绍了Pixhawk硬件和地面站设置过程，有支持的飞机类型的列表。

 

1.2工具链安装
PX4代码可以在 Mac OS, Linux or Windows.上进行开发，建议在Mac OS和Linux上进行开发，因为图像处理和高级导航在windows上不容易开发。如果不确定，新的开发者应默认用Linux和当前Ubuntu长期支持版本（Ubuntu LTS edition）。

开发环境
开发环境安装涉及到下面几种：

Mac OS
Linux
Windows
如果你对Docker熟悉，你可以使用其中一个容器：Docker Containers。

（这里只翻译了Linux下开发环境的搭建）

1.2.1 Linux开发环境搭建（windows等系统的就不翻译了，自己看官网）
我们使用Debian / Ubuntu LTS 作为Linux的标准支持版本，但是但是也支持Cent OS and Arch Linux的发行版本。

权限设置
注意：永远不要使用“sudo”来修复权限问题，否则会带来更多的权限问题，需要重装系统来解决。

把用户添加到用户组“dialout”

[plain] view plain copy 
sudo usermod -a -G dialout $USER  
然后注销后，重新登录，因为重新登录后所做的改变才会有效。

安装
更新包列表，安装下面编译PX4的依赖包。PX4主要支持的家族：

NuttX based hardware: Pixhawk, Pixfalcon
Snapdragon Flight hardware: Snapdragon
Raspberry Pi hardware: Raspberry Pi 2
Host simulation: jMAVSim SITL and Gazebo SITL
注意：安装 Ninja Build System可以比make更快进行编译。如果安装了它就会自动选择使用它进行编译。

[plain] view plain copy 
sudo add-apt-repository ppa:george-edison55/cmake-3.x -y  
sudo apt-get update  
sudo apt-get install python-argparse git-core wget zip \  
    python-empy qtcreator cmake build-essential genromfs -y  
# simulation tools  
sudo apt-get install ant protobuf-compiler libeigen3-dev libopencv-dev openjdk-7-jdk openjdk-7-jre clang-3.5 lldb-3.5 -y  
NuttX的基本硬件
Ubuntu配备了一系列代理管理，这会严重干扰任何机器人相关的串口（或usb串口），卸载掉它也不会有什么影响：

sudo apt-get remove modemmanager
更新包列表和安装下面的依赖包。指明版本的包需要安装指定的版本的包。

[plain] view plain copy 
sudo add-apt-repository ppa:terry.guo/gcc-arm-embedded -y  
sudo apt-get update  
sudo apt-get install python-serial openocd \  
    flex bison libncurses5-dev autoconf texinfo build-essential \  
    libftdi-dev libtool zlib1g-dev \  
    python-empy gcc-arm-none-eabi -y  
如果gcc-arm-none-eabi版本导致PX4/Firmware编译错误，请参考 the bare metalinstallation instructions手动安装4.8版本。

 

1.3编译代码
PX4可以在控制台或者图形界面/IDE开发

在控制台编译
在去到图形界面或者IDE前，验证系统设置的正确性非常重要，因此打开控制台。在Ubuntu，单击启动栏，搜索“terminal”（或者trl+alt+T）。

终端在家目录启动，我们默认去到'~/src/Firmware' 然后，克隆顶层资源库。有经验的开发者可以克隆自己的复制的资源库。

[plain] view plain copy 
mkdir -p ~/src  
cd ~/src  
git clone https://github.com/PX4/Firmware.git  
cd Firmware  
git submodule update --init --recursive  
cd ..  
注意：没有安装git的朋友可以先安装git再执行的命令

[plain] view plain copy
sudo apt-get install git  
现在到时候由源代码编译二进制代码了。但在直接使用硬件前，推荐先进行仿真。喜欢在图形界面开发环境工作的用户也应该继续完成下面部分。

基于NuttX / Pixhawk的硬件板
[plain] view plain copy 
cd Firmware  
make px4fmu-v2_default  
大概10分钟那样就会编译成功，我实在虚拟机的Ubuntu上编译的。编译成功如下图所示

 

注意到“make”是一个字符命令编译工具，“px4fmu-v2”是硬件/ardupilot版本，“default”是默认配置，所有的PX4编译目标遵循这个规则。

成功编译的最后输出是这样的：

[100%] Linking CXX executable firmware_nuttx
[100%] Built target firmware_nuttx
Scanning dependencies of target build_firmware_px4fmu-v2
[100%] Generating nuttx-px4fmu-v2-default.px4
[100%] Built target build_firmware_px4fmu-v2
通过在命令后面添加‘upload’，编译的二进制程序就会通过USB上传到飞控硬件：

make px4fmu-v2_default upload
成功上传的最后输出是这样的：

Erase  : [====================] 100.0%
Program: [====================] 100.0%
Verify : [====================] 100.0%
Rebooting.
 
[100%] Built target upload
 

我使用的是linux开发环境和pixhawk硬件，其他的开发环境和pixhawk的指南我就不翻译了，详细可以参考官网http://dev.px4.io/index.html

1.4 投稿&

核心开发团队和社区的联系信息可以在下面找到。PX4项目使用了三个分支Git branching model:

master 默认情况下不稳定，可以看到快速开发。
beta 已充分测试，面向飞行测试者。
stable 指向最新的发布分支。.
我们尝试通过rebases保持一个线性的历史，避免Githubflow。但是由于全球的开发队伍和快速的开发转移，我们会定期分类合并。

可以这样为代码贡献新功能，可以通过注册Github，复制资源库，创建分支，加入你的改变，最后推送请求。当它们通过我们的持续的综合测试，更新就会被合并。

 

所有的投稿（贡献）必须在 BSD 3-clause license许可下进行，所有的代码在使用上不能提出任何的，进一步的限制。

测试飞行结果
飞行测试对于保证质量非常重要，请从microSD卡上传飞行日志到 Log Muncher，并在 论坛分享一个的连接，附带有书面飞行报告。

论坛和聊天
Google+
Gitter
PX4 Users Forum
每周开发电话

PX4团队在每周同时进行电话联系（通过Mumble客户端连接）。

TIME: 19:00h Zurich time, 1 p.m. Eastern Time, 10 a.m. Pacific Standard Time
Server: mumble.dronecode.org
Port: 10028
Channel: PX4 Channel
The agenda is announced the same day on the px4users forum
