# uORB消息机制

官网英文原文地址：http://dev.px4.io/advanced-uorb.html

## 简介

uORB是一种用于进程间进行异步发布和订阅的消息机制API。

在[tutorial](tutorial-hello-sky.md)中可以学习通过C++如何使用uORB。

uORB在bootup之前自动运行，很多应用基于uORB。（我认为是在系统启动前执行uorb并注册各自应用这个bootup应该是指的nuttx系统启动，彩虹小羊注）。uORB通过`uorb start`启动。在Unit test中可以使用`uorb test`开始uorb。

## 添加新的topic


要想增加新的topic，你需要在‘msg/’目录下创建一个新的‘.msg’ 文件并在`msg/CMakeLists.txt`下添加该文件。这样C/C++编译器自动在程序中添相应的代码。
可以先看看现有的'msg'文件了解下都支持那些类型。一个消息也可以嵌套在其他消息当中。
每一个生成的C/C++结构体中，一个field `uint64_t timestamp` 会被增加。这个变量用于将消息记录到日志当中
为了在代码中使用"topic"需要添加头文件:

```
#include <uORB/topics/topic_name.h>
```

在文件`.msg`中，通过添加类似如下的一行代码,一个消息定义就可以用于多个独立的主题.

```
# TOPICS mission offboard_mission onboard_mission
```

然后在代码中, 把它们作为主题id使用:`ORB_ID(offboard_mission)`.

## 发布主题

在系统的任何地方都可以发布一个主题, 包括在中断上下文中(被`hrt_call`接口调用的函数). 但是, 广播一个主题仅限于在中断上下文之外.
一个主题只能由同一个进程进行广播, 并作为其之后的发布.

## 列出所有主题并进行监听

‘收听者’命令仅在Pixracer（FMUv4）以及Linux/OS X上可用。

要列出所有主题, 先列出文件句柄:

```sh
ls /obj
```

要列出一个主题中的5个消息, 执行以下监听命令:

```sh
listener sensor_accel 5
```

得到的输出就是关于该主题的n次内容:

```sh
TOPIC: sensor_accel #3
timestamp: 84978861
integral_dt: 4044
error_count: 0
x: -1
y: 2
z: 100
x_integral: -0
y_integral: 0
z_integral: 0
temperature: 46
range_m_s2: 78
scaling: 0

TOPIC: sensor_accel #4
timestamp: 85010833
integral_dt: 3980
error_count: 0
x: -1
y: 2
z: 100
x_integral: -0
y_integral: 0
z_integral: 0
temperature: 46
range_m_s2: 78
scaling: 0
```

