# uORB Messaging
# uORB消息机制
## Introduction
## 简介

The uORB is an asynchronous publish() / subcribe() messaging API used forinter-thread/inter-process communication.
Look at the [tutorial](tutorial-hello-sky.md) to learn how to use it in C++.
uORB is automatically started early on bootup as many applications depend on it.
It is started with `uorb start`. Unit tests can be started with `uorb test`.
uORB是一种用于进程间进行异步发布和订阅的消息机制API。
在[tutorial](tutorial-hello-sky.md)中可以学习通过C++如何使用uORB。
uORB在bootup之前自动运行，很多应用基于uORB。（我认为是在系统启动前执行uorb并注册各自应用这个bootup应该是指的nuttx系统启动，彩虹小羊注）。uORB通过`uorb start`启动。在Unit test中可以使用`uorb test`开始uorb。
## Adding a new topic
## 添加新的topic
##
To add a new topic, you need to create a new `.msg` file in the `msg/`
directory and add the file name to the `msg/CMakeLists.txt` list. From this,
there will automatically be C/C++ code generated.
Have a look at the existing `msg` files for supported types. A message can also
be used nested in other messages.

To each generated C/C++ struct, a field `uint64_t timestamp` will be added. This
is used for the logger, so make sure to fill it in when logging the message.
To use the topic in the code, include the header:
要想增加新的topic，你需要在‘msg/’目录下创建一个新的‘.msg’ 文件并在`msg/CMakeLists.txt`下添加该文件。这样C/C++编译器自动在程序中添相应的代码。
可以先看看现有的'msg'文件了解下都支持那些类型。一个消息也可以嵌套在其他消息当中。
每一个生成的C/C++结构体中，一个field `uint64_t timestamp` 会被增加。这个变量用于将消息记录到日志当中
为了在代码中使用"topic"需要添加头文件:
```
#include <uORB/topics/topic_name.h>
```

By adding a line like the following in the `.msg` file, a single message
definition can be used for multiple independent topic instances:
通过在".msg"文件中添加像下面这行代码，一个消息就可以在多个独立的topic中使用了。
```
# TOPICS mission offboard_mission onboard_mission
```

Then in the code, use them as topic id: `ORB_ID(offboard_mission)`.

## Publishing

Publishing a topic can be done from anywhere in the system, including interrupt context (functions called by the `hrt_call` API). However, advertising a topic is only possible outside of interrupt context. A topic has to be advertised in the same process as its later published.

## Listing Topics and Listening in

<aside class="note">
The 'listener' command is only available on Pixracer (FMUv4) and Linux / OS X.
</aside>

To list all topics, list the file handles:

```sh
ls /obj
```

To listen to the content of one topic for 5 messages, run the listener:

```sh
listener sensor_accel 5
```

The output is n-times the content of the topic:

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

