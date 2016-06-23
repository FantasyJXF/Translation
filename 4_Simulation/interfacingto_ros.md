# ROS仿真接口

模拟自驾仪会在端口14557开放第二个MAVLink接口。将MAVROS连接到这个端口将会接收到实际飞行中发出的所有数据。

## 启动MAVROS

如果需要ROS接口，那么已经在运行的次要MAVLink实例可以通过[mavros](../10_Robotics-using-ROS/ros-mavros-offboard.md)连接到ROS。使用如下的URL连接到指定IP（`fcu_url`即SITL的地址和端口）：

<div class="host-code"></div>

```sh
roslaunch mavros px4.launch fcu_url:="udp://:14540@192.168.1.36:14557"
```

使用这个URL连接到本地：

<div class="host-code"></div>

```sh
roslaunch mavros px4.launch fcu_url:="udp://:14540@127.0.0.1:14557"
```

## 为ROS安装Gazebo

Gazebo ROS SITL仿真可以在Gazebo6正常运行（Gazebo7不能正常运行），使用如下命令安装Gazebo6：

```sh
sudo apt-get install ros-indigo-gazebo6-ros
```

如果需要使用其它传感器模型（例如激光），那么还需要安装Gazebo插件：

```sh
sudo apt-get install ros-indigo-gazebo6-plugins
```

## 启动ROS包装过的Gazebo

如果想要修改Gazebo仿真，使其能够将额外的传感器信息直接发布到ROS主题，例如Gazebo ROS激光传感器信息，那么必须通过适当的ROS包装器来启动Gazebo。

下面是一些可用的ROS启动脚本，这些脚本可以在ROS中运行包装过的仿真。

- [posix_sitl.launch](https://github.com/PX4/Firmware/blob/master/launch/posix_sitl.launch): 简单SITL
- [mavros_posix_sitl.launch](https://github.com/PX4/Firmware/blob/master/launch/mavros_posix_sitl.launch): SITL和MAVROS

为了在ROS中运行包装过的SITL，需要升级ROS环境，然后正常启动：

```sh
cd <Firmware_clone>
source integrationtests/setup_gazebo_ros.bash $(pwd)
roslaunch px4 posix_sitl.launch
```

在自己的启动文件中包含上面提到的启动文件中的任意一个就可以在仿真中运行自己的ROS应用。

### 背后的细节

(或者如何手动运行)

```sh
no_sim=1 make posix_sitl_default gazebo
```

这将启动仿真器，控制台看上去是这样的：

```sh
[init] shell id: 46979166467136
[init] task name: mainapp

______  __   __    ___
| ___ \ \ \ / /   /   |
| |_/ /  \ V /   / /| |
|  __/   /   \  / /_| |
| |     / /^\ \ \___  |
\_|     \/   \/     |_/

Ready to fly.


INFO  LED::init
729 DevObj::init led
736 Added driver 0x2aba34001080 /dev/led0
INFO  LED::init
742 DevObj::init led
INFO  Not using /dev/ttyACM0 for radio control input. Assuming joystick input via MAVLink.
INFO  Waiting for initial data on UDP. Please start the flight simulator to proceed..
```

现在，在一个新的终端中确保你可以通过Gazebo菜单插入Iris模型，这需要设置环境变量，在其中包含适当的`sitl_gazebo`文件夹。

```sh
cd <Firmware_clone>
source integrationtests/setup_gazebo_ros.bash $(pwd)
```

现在，就像在ROS中做过的那样启动Gazebo，并在其中插入Iris四旋翼模型。一旦Iris模型载入，它将会自动连接到px4应用。

```sh
roslaunch gazebo_ros empty_world.launch world_name:=$(pwd)/Tools/sitl_gazebo/worlds/iris.world
```