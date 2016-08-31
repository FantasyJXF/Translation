# **骁龙Flight的光流**
官网英文原文地址：http://dev.px4.io/optical_flow.html

骁龙Flight有一个向下的灰度摄像头可用于实现基于光流的定位。

除了一个摄像头，光流还需要一个向下的距离传感器.这里讨论关于TeraRanger One的使用。

## **安装TeraRanger One **

为了将TeraRanger One(TROne)连接到骁龙Flight上，必须使用TROne I2C适配器。 TROne必须先由供应商刷好I2C固件。

TROne通过一根定制的DF13 4转6接口线与骁龙Flight连接。线序如下：
| **4 pin** | **&lt;-&gt;** | **6 pin** |
| :--- | :--- | :--- |
| 1 |  | 1 |
| 2 |  | 6 |
| 3 |  | 4 |
| 4 |  | 5 |

TROne必须用10-20V的电压供电。

## **光流**
光流获取的数据在应用处理器上进行计算并通过Mavlink传到PX4中。从GitHub上克隆[snap_cam]（https://github.com/px4/snap_cam）仓库的固件并根据README文件中的教程完成编译。

运行光流的应用程序来root：

```
optical_flow -n 50 -f 30
```

光流应用要求IMU mavlink消息从PX4。您可能需要添加一个额外的mavlink实例PX4通过添加以下内容到你的\` mainapp.config \`:
光流的应用程序来自PX4的IMU MavLink消息。你可能需要通过添加下列代码到你的`mainapp.config`中来添加一个MavLink例程到PX4中。  

```
mavlink start -u 14557 -r 1000000 -t 127.0.0.1 -o 14558
mavlink stream -u 14557 -s HIGHRES_IMU -r 250
```



