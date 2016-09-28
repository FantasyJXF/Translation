# Gazebo仿真
---
官网英文原文地址：http://dev.px4.io/simulation-gazebo.html
# Gazebo 仿真

[Gazebo](http://gazebosim.ort)是一个自主机器人3D仿真环境。它可以与ROS配套用于完整的机器人仿真，也可以单独使用。本文简要介绍单独的使用方法。

{% raw %}
<video id="my-video" class="video-js" controls preload="auto" width="100%" 
poster="../pictures/diagrams/PX4-Flight.JPG" data-setup='{"aspectRatio":"16:9"}'>
  <source src="http://7xvob5.com1.z0.glb.clouddn.com/2-PX4%20Flight%20Stack%20ROS%203D%20Software%20in%20the%20Loop%20Simulation%20(SITL).mp4" type='video/mp4' >
  <p class="vjs-no-js">
    To view this video please enable JavaScript, and consider upgrading to a web browser that
    <a href="http://videojs.com/html5-video-support/" target="_blank">supports HTML5 video</a>
  </p>
</video>
{% endraw %}

{% mermaid %}
graph LR;
  Gazebo-->插件;
  插件-->MAVLink;
  MAVLink-->SITL;
{% endmermaid %}


## 安装

需要安装Gazebo和我们的仿真插件。


> **提示** 推荐使用Gazebo7（最低使用Gazebo6）。如果你的Linux操作系统安装的ROS版本早于Jade，请先卸载其绑定的旧版本Gazebo (sudo apt-get remove ros-indigo-gazebo)，因为该版本太老了。


### Mac OS

Mac OS 安装 Gazebo 6。

<div class="host-code"></div>

```sh
brew install gazebo6
```

### Linux

PX4 SITL使用Gazebo仿真软件，但不依赖ROS。但是也可以像普通飞行代码一样[与ROS连接](../4_Simulation/interfacingto_ros.md)进行仿真。

#### ROS 用户

如果你计划与ROS一起用 PX4，确保按照[Gazebo 6版本指南](http://gazebosim.org/tutorials?tut=ros_wrapper_versions#Gazebo6.xseries)进行配置。

#### 正常安装

按照[Linux安装指导](http://gazebosim.org/tutorials?tut=install_ubuntu&ver=6.0&cat=install) 安装Gazebo 6。

## 进行仿真

在PX4固件源文件的目录下运行一种机架类型（支持四旋翼、固定翼和垂直起降，含光流）的PX4 SITL。

### 四旋翼

<div class="host-code"></div>

```sh
cd ~/src/Firmware
make posix_sitl_default gazebo
```

### 四旋翼带光流模块

<div class="host-code"></div>

```sh
cd ~/src/Firmware
make posix gazebo_iris_opt_flow
```

### 垂直起降

<div class="host-code"></div>

```sh
cd ~/src/Firmware
make posix_sitl_default gazebo_standard_vtol
```

### 立式垂直起降

<div class="host-code"></div>

```sh
cd ~/src/Firmware
make posix_sitl_default gazebo_tailsitter
```

<aside class="tip">
如果你在运行的时候遇到错误或缺少依赖，确保你是按照[安装文件和代码](http://dev.px4.io/starting-installing-mac.html)安装的。
<aside>

接着会启动PX4 shell:

```sh
[init] shell id: 140735313310464
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

<aside class="note">
右击四旋翼模型可以从弹出的菜单中启用跟随模式，这将会始终保持飞行器在视野中。
</aside>

## 起飞

 ![gazebo](../pictures/sim\gazebo.png)

一旦完成初始化，系统将会打印出起始位置(`telem> home: 55.7533950, 37.6254270, -0.00`)。你可以通过输入下面的命令让飞行器起飞：

```sh
pxh> commander takeoff
```

<aside class="tip">
可以通过QGroundControl (QGC)支持手柄或拇指手柄。为了使用手柄控制飞行器，要将系统设为手动飞行模式（如 POSCTL、位置控制），并从QGC的选项菜单中启用拇指手柄。
</aside>

## 扩展和定制

为了扩展和定制仿真接口，编辑`Tools/sitl_gazebo`文件夹中的文件。这些代码可以从Github上的[sitl_gazebo repository](https://github.com/px4/sitl_gazebo)访问。

<aside class="note">
构建系统强制检查所有依赖的子模块，包括仿真软件。虽然这些文件夹中文件的改变不会被覆盖，但当这些改变被提交的时候子模块需要在固件库中以新的hash注册。为此，输入`git add Tools/sitl_gazebo` 进行提交。这样仿真软件的GIT hash就会被更新。
</aside>

## 与ROS连接

仿真可以像真实的飞控一样[与ROS连接](../4_Simulation/interfacingto_ros.md)
