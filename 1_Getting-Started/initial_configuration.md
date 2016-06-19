# 初始设置

在开始开发PX4之前，系统需要按照默认配置进行初始配置，以保证硬件设置合适，被检测到。下面的一个视频介绍[Pixhawk硬件](../5_Autopilot-Hardware/pixhawk.md)和[地面站](../5_Autopilot-Hardware/pixhawk.md)设置过程，[下面](../7_Airframe/airframes-architecture.md)是支持的参考机架类型的列表。


<aside class="tip">
[下载每日更新的QGroundControl](http://qgroundcontrol.org/downloads) 并按照下面的说明来设置你的飞行器。参考  [QGroundControl 教程](../3_Tutorial/ground_control_station.md) 来了解任务规划，放飞和和参数设置的具体细节。
 
</aside>

下面的视频介绍一系列的设置选项

{% youtube %}https://www.youtube.com/watch?v=91VGmdSlbo4&rel=0&vq=hd720{% endyoutube %}

## 无线电控制选项

PX4飞行栈并不强制要求无线控制系统。它也不要求使用单独的开关选择飞行模式。

### 没有无线电控制的飞行

所有的无线电控制装置的检查可以通过设置参数`COM_RC_IN_MODE`为` 1 `禁用。这将不允许手动飞行，但是，除了比如flying in。

### 单通道模式开关

<aside class="todo">
Move these instructions here.
</aside>

在这种模式下，该系统接受一个单一的通道作为模式开关，而不是使用多个开关，这在 [legacy wiki](https://pixhawk.org/peripherals/radio-control/opentx/single_channel_mode_switch)有解释。

