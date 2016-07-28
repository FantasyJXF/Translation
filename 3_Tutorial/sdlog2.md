# sdlog2

---

官网英文原文地址：https://pixhawk.org//firmware//apps//sdlog2

`sdlog2` 这个应用程序是用来将FMU的飞行数据记录到SD卡中作为日志文件。该日志文件的格式与APM的二进制日志格式兼容，但是''sdlog2'' 使用强制消息''TIME'' 来写时间戳。

## 使用

每当 `sdlog2` 开始记录时， 会在 SD卡的 `log` 文件夹中创建一个新的目录。 如果设置了选项`-t` 同时有一个GPS的时间戳可用的话，文件夹的命名是基于当前的日期的\(例如， `log/2014-01-19`\)。否则，文件夹就会被命名为 ''sessXXX''，这里`XXX`代表一个序列号。如果可能并且可以使用`t`选项的话，文件名的创建与使用当前时间命名的方式相似\(例如，`log/2014-01-19/19_37_52.bin`\) 否则这个文件就命名为 `log.XXX.bin` ，再次使用序列号。

根据给定的选项开始记录日志，要么当 `sdlog2` 应用程序启动，要么当系统解锁，或者通过mavlink命令。

```
usage: sdlog2 {start|stop|status} [-r <log rate>] [-b <buffer size>] -e -a
        -r      Log rate in Hz, 0 means unlimited rate
        -b      Log buffer size in KBytes, default is 8
        -e      Enable logging on app start (if not, can be started by command)
        -a      Log only when armed (can be still overriden by command)
        -t      Use GPS timestamps to create folder and file names
```



> 该日志记录的性能取决于所使用的microSD卡。建议使用高质量的SD卡以避免遗漏数据。 虽说不会对飞行性能产生负面的影响，但是全速运行"sdlog2"应用程序（即不加`-l`选项）可能会引起巨大的CPU负载。然而，所需的全速可能无法得到满足，日志记录也将会跳过一些消息。

# 开始记录日志


在一般情况下，飞行器解锁之后就会开始记录日志，因为只有激活了的飞行才值得分析。如果要手动启动日志记录，可以在console控制台输入以下指令：

```
sdlog2 stop

sdlog2 start -t -r 200 -e -b 16
```

要停止记录则输入:

```
sdlog2 stop
```

# 分析日志文件

## FlightPlot

要查看以及分析日志，可以使用GUI工具 [FlightPlot](https://pixhawk.org/dev/flightplot) 。它可以无需转换地读取 `sdlog2`生成的日志文件.。

## Pymavlink

你也可以使用包含在 [PyMAVLink](https://pixhawk.org/dev/pymavlink) 中的mavgraph工具来生成绘图。

## CSV \/ Matlab: Converting Logs to CSV

要读取二进制文件并将其转换为CSV，可以使用Python工具[sdlog2\_dump.py](https://github.com/PX4/Firmware/tree/master/Tools/sdlog2)。同样的目录中包含着运行转换器和绘制大量核心信息的MATLAB脚本。


例如：要读取`TIME`和`IMU`的消息，`SENS`消息中的`BaroAlt`和`BaroTemp`数据，请使用下面的指令：

```
python sdlog2_dump.py log001.px4log -t TIME -m TIME -m IMU -m SENS.BaroAlt,BaroTemp
```

要创建CSV直接重定向输出到文件：

```
python sdlog2_dump.py log001.px4log -t TIME -m TIME -m IMU -m SENS.BaroAlt,BaroTemp > log.csv
```

CSV文件中的列将与参数具有相同的顺序。选项`t`显著的减少了输出的重复数据，应该始终被`sdlog2`生成的日志记录文件使用。但是不要用在原来的APM日志文件中。

### 消息类型举例

* TIME - 时间戳
* ATT  - 飞行器的姿态
* ATSP - 飞行器的姿态设定值
* IMU  - IMU传感器
* SENS - 其他传感器
* LPOS - 本地位置估计
* LPSP - 本地位置设定值
* GPS  - GPS位置
* ATTC - 姿态控制 \(actuator\_0 output\)
* STAT - 飞行器的状态
* RC   - 遥控输入通道
* OUT0 - Actuator\_0 output
* AIRS - 空速
* ARSP - 角速度设定值
* FLOW - 光流
* GPOS - 全球位置估计
* GPSP - 全球位置设定值
* ESC  - 电调状态
* GVSP - 全球速度设定值

> 消息类型有时是可以改变的。为了找出包含在日志文件中的实际消息列表，可以使用以下的指令：
> 
> ```
> python sdlog2_dump.py log001.bin -v
> ```
> 
> 或者使用[FlightPlot](https://pixhawk.org/dev/flightplot)来查看。

## 发现并修理故障


为了防止在飞行过程中有大量的IO操作时关键的应用程序终止， `sdlog2`在接收消息和将日志写到SD卡之间设置了一个缓冲区 。如果缓冲器在一些点溢出了，一些日志消息将被被跳过。跳过的消息数可以通过在控制台中使用`sdlog2 status`指令进行检查。以及关闭日志文件时打印的统计数字，即，在解锁时使用的`-a`选项。如果跳过的消息数不是0，就可以通过在`-b`选项中增加缓冲器大小进行修复。以下指令将设置日志缓冲区的大小为16KiB,而不是默认的8KiB:

```
sdlog2 start -t -r 100 -e -b 16
```

## 负载测试

为了测试microSD的带宽，以200Hz的频率32KB的缓冲区开启该应用程序，键入`-e`标志立刻开始记录日志。

```
首先停止正在运行的实
# sdlog2 stop
sdlog2 start -t -r 200 -e -b 32
# Run the perf command to see the induced sdlog2 load:
# (NOTE: the performance counter only exists during logging)
perf
# Or run top
top
# Stop the app to clean up FDs and filesystem
sdlog2 stop
```

## 日志文件样本

* `downloads` [sdlog2\_sample\_log\_001.bin.zip](http://7xvob5.com1.z0.glb.clouddn.com/sdlog2_sample_log_001.bin.zip)- captured at 100 Hz rate

