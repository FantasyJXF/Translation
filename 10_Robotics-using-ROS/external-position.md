# 使用视觉或运动捕捉系统

官网英文原文地址：http://dev.px4.io/external-position.html

本文旨在使用除GPS之外的位置数据源构建一个基于PX4的系统，例如像VICON、Optitrack之类的运动捕捉系统和像[ROVIO](https://github.com/ethz-asl/rovio)、[SVO](https://github.com/uzh-rpg/rpg_svo)或者[PTAM](https://github.com/ethz-asl/ethzasl_ptam)之类的基于视觉的估计系统。

位置估计既可以来源于板载计算机，也可以来源于外部系统（例如：VICON）。这些数据用于更新机体相对于本地坐标系的位置估计。来自于视觉或者运动捕捉系统的朝向信息也可以被适当整合进姿态估计器中。

现在，这个系统被用来进行室内位置控制或者基于视觉的路径点导航。

对于视觉，用来发送位姿数据的MAVLink消息是[VISION_POSITION_ESTIMATE](http://mavlink.org/messages/common#VISION_POSITION_ESTIMATE)。对于运动捕捉系统，相应的则为[ATT_POS_MOCAP](http://mavlink.org/messages/common#ATT_POS_MOCAP)。

默认发送这些消息的应用是ROS-Mavlink接口MAVROS，当然，也可以直接使用纯C/C++代码或者MAVLink()库来发送它们。

## 使能外部位姿输入

需要设置两个参数（从QGroundControl或者NSH shell）来使能或者禁用视觉/运动捕捉。


> 设置系统参数```CBRK_NO_VISION```为0来使能视觉位置估计。 

> 设置系统参数```ATT_EXT_HDG_M```为1或者2来使能外部朝向估计。设置为1使用视觉，设置为2使用运动捕捉。
