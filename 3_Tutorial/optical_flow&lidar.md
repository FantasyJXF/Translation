# 光流传感器和激光雷达传感器

官网英文原文地址：http://dev.px4.io/flow_lidar_setup.html

本页展示了如何在INAV位置估计中设置PX4FLOW光流传感器和LIDAR-Liter激光雷达传感器。如下是一个定位的验证小视频。

* [室内](http://7xvob5.com2.z0.glb.qiniucdn.com/Pixhawk%20PX4Flow%20indoor%20demo.mp4) 
* [室外](http://7xvob5.com2.z0.glb.qiniucdn.com/Pixhawk%20PX4Flow%20outdoor%20demo.mp4)

  ![光流](../pictures/hardware\px4flow.png)
  ![激光雷达](../pictures/hardware\lidarlite.png)


## 硬件

PX4Flow光流传感器必须指向地面，并且可以使用I2C口连接到pixhawk。

关于LIDAR-Liter激光雷达传感器的连接，请查看页面[this](https://pixhawk.org/peripherals/rangefinder?s[]=lidar).
为了得到最好的性能，请保持PX4Flow光流传感器在一个良好的位置并且不要有太大的震动。（最好在四旋翼飞行器的下方且朝向地面）

![flow_lidar_attached](../pictures/hardware\flow_lidar_attached.jpg)

## 参数

所有的参数都能通过QGroundControl地面站进行修改

* SENS\_EN\_LL40LS
  设置为1来启用激光雷达距离测量

* INAV\_LIDAR\_EST
  设置为1来启用基于距离测量的高度估计。

* INAV\_FLOW\_DIST\_X and INAV\_FLOW\_DIST\_Y
  这两个参数（单位：米）被用来作偏航角度的补偿。
  补偿的偏移量参考下面的图例。
   ![flowing](../pictures/hardware\px4flow_offset.png)

* ​
  上面的例子中，光流传感器的安装位置会产生一个正向的X轴的偏移和一个正向的Y轴的偏移。

* INAV\_LIDAR\_OFF
  是指激光雷达高度的一个振动偏移量（单位/米），这个数值会被加入到激光定高传感器的距离测量中。


## 高级用户

对于高级用户，下面的参数也可以进行修改。但在你了解它们的用法前，不要修改它们。

* INAV\_FLOW\_W
  是指光流的位置估计对整个姿态估计的权重。
* INAV\_LIDAR\_ERR
  设置高度估计的门限值，如果测量结果值高于这个值，测量结果会被舍弃。

