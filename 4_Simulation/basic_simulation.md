# 基本仿真

# 循环仿真软件 (SITL) 

循环仿真软件运行在主机上的完整的系统并模拟自动驾驶仪。它通过本地网络连接到模拟器。 设置看起来像这样 


{% mermaid %}
graph LR;
  Simulator-->MAVLink;
  MAVLink-->SITL;
{% endmermaid %}
## 运行循环仿真软件 SITL

在确保[模拟必备条件](../1_Getting-Started/install_toolchain.md) 已经安装在系统上, 刚刚推出 : 使方便目标将编译在POSIX主机建立和运行仿真 .

<div class="host-code"></div>

```sh
make posix_sitl_default jmavsim
```

这将提到PX4 shell:

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

## 重要文件

- 启动脚本文件是在 [posix-configs/SITL/init](https://github.com/PX4/Firmware/tree/master/posix-configs/SITL/init) 文件夹和名叫 `rcS_SIM_AIRFRAME`, 默认是 `rcS_jmavsim_iris`.
- 系统启动文件 (相当于 `/` 认为这) 位于生成内部的目录 : `build_posix_sitl_default/src/firmware/posix/rootfs/`

## 带着它去空中

在一个带3D图像的窗口的 [jMAVSim](http://github.com/PX4/jMAVSim.git)模拟器:

 ![jmavsim](../pictures/sim\jmavsim.png)
一旦完成初始化，该系统将登载原点 (`telem> home: 55.7533950, 37.6254270, -0.00`). 你能够通过输入，验证命令在空中生效：

```sh
pxh> commander takeoff
```

> <aside class="tip">
> 可通过地面站(QGC)支持操纵杆或拇指操纵杆 . 要使用手动输入，把系统打在手动飞行模式  (e.g. POSCTL, 位置控制). 从地面站QGC参考菜单启动拇指操纵杆。
> </aside>
>

## 模拟一个Wifi无人机

用一个特殊的目标来模拟无人机通过局域网WiFi连接 :

<div class="host-code"></div>

```sh
make broadcast jmavsim
```

模拟器在本地网络的他的地址上传输就像一个真无人机所能做的一样。

## 扩展和自定义

扩展或自定义仿真界面 , 编辑文件在 `Tools/jMAVSim` 文件夹。代码能够通过 [jMAVSim repository](https://github.com/px4/jMAVSim)  Github网取得原码.

<aside class="note">
建立系统的执行正确的模块来检测依赖项 ，包括模拟器. 它不会覆盖目录中的文件的更改, 然而, 当这些改变是继承了需要在固件中重新获得新的HASH协议注册的子模块. 这样做, `git add Tools/jMAVSim` 和继承修改. 这将更新模拟器的 GIT hash.
</aside>

## Interfacing to ROS

模拟能够 [interfaced to ROS](../4_Simulation/interfacingto_ros.md) 同样的方式像车载真车一样 .
