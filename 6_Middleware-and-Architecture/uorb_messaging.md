# uORB消息收发

## 介绍

uORB是一个异步的，用于线程间或者进程间通信的消息发布/订阅接口.

查看 [教程](../3_Tutorial/writing_an_application.md),学习如何在C++中使用它.


uORB会在系统启动的早期自动运行，因为很多应用程序依赖于它. 运行的命令是 `uorb start`.
单元测试可以运行 `uorb test`.

## 添加一个新主题

要添加一个新主题, 你需要在`msg/`目录创建新一个文件`.msg`,然后添加该文件名到
`msg/CMakeLists.txt`列表. 这样,关于主题的C/C++代码就会被自动生成.

查看已有的`msg`文件中可以看到支持的消息类型. 一个消息也可以嵌套在另外的消息中.
在每一个被生成的C/C++结构中，字段`uint64_t timestamp`都会被添加. 该字段是用于日志记录的，
所以当记录消息的时候请确保对它有赋值.

要在代码中使用主题，首先添加头文件:

```
#include <uORB/topics/topic_name.h>
```

在文件`.msg`中，通过添加类似如下的一行代码,一个消息定义就可以用于多个独立的主题.

```
# TOPICS mission offboard_mission onboard_mission
```

然后再代码中, 把它们作为主题id使用:`ORB_ID(offboard_mission)`.

## 发布主题

在系统的任何地方都可以发布一个主题, 包括在中断上下文中(被`hrt_call`接口调用的函数). 但是, 广播一个主题仅限于在中断上下文之外.
一个主题只能由同一个进程进行广播, 并作为其之后的发布.

## 列出所有主题并进行监听

<aside class="note">
The 'listener' command is only available on Pixracer (FMUv4) and Linux / OS X.
</aside>

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

