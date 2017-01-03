# 初始设置
官网英文原文地址： http://dev.px4.io/starting-initial-config.html

在开始开发PX4之前，系统需要按照默认配置进行初始配置，以保证硬件设置合适，被检测到。下面的一个视频介绍[Pixhawk硬件](../5_Autopilot-Hardware/pixhawk.md)和[地面站](../5_Autopilot-Hardware/pixhawk.md)设置过程，[下面](../7_Airframe/airframes-architecture.md)是支持的参考机架类型的列表。

>[下载DAILY BUILD版本的QGroundControl](http://qgroundcontrol.org/downloads)并按照下面的说明来设置你的飞行器。参考[QGroundControl 教程](../3_Tutorial/ground_control_station.md)来了解任务规划，放飞和和参数设置的具体细节。
 

下面的视频介绍一系列的设置选项

{% raw %}
<video id="my-video" class="video-js" controls preload="auto" width="100%" 
poster="http://image84.360doc.com/DownloadImg/2015/04/1617/52474470_2.jpg" data-setup='{"aspectRatio":"16:9"}'>
  <source src="http://7xvob5.com1.z0.glb.clouddn.com/1-PX4%20Autopilot%20Setup%20Tutorial%20Preview.mp4" type='video/mp4' >
  <p class="vjs-no-js">
    To view this video please enable JavaScript, and consider upgrading to a web browser that
    <a href="http://videojs.com/html5-video-support/" target="_blank">supports HTML5 video</a>
  </p>
</video>
{% endraw %}

## 无线电控制选项

PX4飞行控制栈并不强制要求无线电控制系统。也不要求使用单独的开关来选择飞行模式。

### 没有无线电控制的飞行

所有的无线电控制装置的检查可以通过设置参数`COM_RC_IN_MODE`为` 1 `禁用。这将不允许手动飞行，但是，除了比如flying in。

### 单通道模式开关

在这种模式下，系统将接受一个单一的通道作为模式开关，而不是使用多个开关，这在 [legacy wiki](https://pixhawk.org/peripherals/radio-control/opentx/single_channel_mode_switch)有解释。


