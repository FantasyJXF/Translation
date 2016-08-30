# **在骁龙飞行的光流**
官网英文原文地址：http://dev.px4.io/optical_flow.html

骁龙飞行板有向下的灰度摄像头可用于基于位置稳定的光流。除了一个摄像头，光流需要一个向下的面对距离传感器。这里的teraranger一讨论使用。

## **TeraRanger的安装程序 **

连接teraranger人（特朗\）的骁龙飞行的特朗I2C适配器必须使用。在特朗必须亮出与供应商的I2C固件。这不是通过自定义DF134-to-6引脚连接电缆的骁龙飞行。该接线如下： 
| **4 pin** | **&lt;-&gt;** | **6 pin** |
| :--- | :--- | :--- |
| 1 |  | 1 |
| 2 |  | 6 |
| 3 |  | 4 |
| 4 |  | 5 |

特朗所需电压10——20V。

## **光流**
光流计算的应用处理器，通过Mavlink送到PX4。克隆和编译[卡\ _cam ]（https://github.com/px4/snap_cam）据其自述说明回购。

以光流应用程序作为根：

```
optical_flow -n 50 -f 30
```

光流应用要求IMU mavlink消息从PX4。您可能需要添加一个额外的mavlink实例PX4通过添加以下内容到你的\` mainapp.config \`:

```
mavlink start -u 14557 -r 1000000 -t 127.0.0.1 -o 14558
mavlink stream -u 14557 -s HIGHRES_IMU -r 250
```



