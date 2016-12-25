# 相机触发器

官网英文原文地址:http://dev.px4.io/advanced-camera-trigger.html

相机触发驱动器是为了让AUX端口发出一个脉冲来触发相机. 这个可以用于多个应用程序，包括航测和重建照片、同步多相机系统或者视觉惯性导航.

除了会发送一个脉冲, mavlink会传回一个序列号（当前所拍摄的图像的一个序列号）和拍摄的时间.
支持三种不同的模式:

- `触发` 1 就像一个基本的定时器，可以通过系统控制台分别设置启用和禁用 `相机触发使能` 或 `相机触发不使能`. 可以重复设置间隔时间，来执行相机触发.
- `触发模式` 2 用一个开关来定时何时曝光.
- `触发模式` 3 基于远程触发.每次超过设定的水平距离时都要触发拍摄
. 然而，两个相机之间的最小时间间隔由设定的触发间隔时间决定.

在 `触发模式` 0 触发关闭.

The full list of parameters pertaining to the camera trigger module can be found
on the想找到与相机触发模块有关的参数配置的完整列表，请参考 [参考](https://pixhawk.org/firmware/parameters#camera_trigger) 页.


> If it is your first time enabling the camera trigger app, remember to reboot
after changing the `TRIG_MODE` parameter to either 1, 2 or 3.


## Camera-IMU sync example

In this example, we will go over the basics of synchronizing IMU measurements with visual data to build a stereo Visual-Inertial Navigation System (VINS). To be clear, the idea here isn't to take an IMU measurement exactly at the same time as we take a picture but rather to correctly time stamp our images so as to provide accurate data to our VI algorithm.

The autopilot and companion have different clock bases (boot-time for the autopilot and UNIX epoch for companion), so instead of skewing either clock, we directly observe the time offset between the clocks. This offset is added or subtracted from the timestamps in the mavlink messages (e.g HIGHRES_IMU) in the cross-middleware translator component (e.g Mavros on the companion and mavlink_receiver in PX4). The actual synchronisation algorithm is a modified version of the Network Time Protocol (NTP) algorithm and uses an exponential moving average to smooth the tracked time offset. This synchronisation is done automatically if Mavros is used with a high-bandwidth on-board link.
For acquiring synchronised image frames and inertial measurements, we connect the trigger inputs of the two cameras to a GPIO pin on the autopilot. The timestamp of the inertial measurement from mid-exposure, and a image sequence number is recorded and sent to the companion computer (CAMERA_TRIGGER message), which buffers these packets and the image frames acquired from the camera. They are matched based on the sequence number, the images timestamped (with the timestamp from the CAMERA_TRIGGER message) and then published.

The following diagram illustrates the sequence of events which must happen in order to correctly timestamp our images.

{% mermaid %}
sequenceDiagram
  Note right of px4 : Time sync with mavros is done automatically
  px4 ->> mavros : Camera Trigger ready
  Note right of camera driver : Camera driver boots and is ready
  camera driver ->> mavros : mavros_msgs::CommandTriggerControl
  mavros ->> px4 : MAVLink::MAV_CMD_DO_TRIGGER_CONTROL
  loop Every TRIG_INTERVAL milliseconds
  px4 ->> mavros : MAVLink::CAMERA_TRIGGER
  mavros ->> camera driver : mavros_msgs::CamIMUStamp
  camera driver ->> camera driver : Match sequence number
  camera driver ->> camera driver : Stamp image and publish
end
{% endmermaid %}

### Step 1

First, set the TRIG_MODE to 1 to make the driver wait for the start command and
reboot your FCU to obtain the remaining parameters.

### Step 2

For the purposes of this example we will be configuring the trigger to operate
in conjunction with a Point Grey Firefly MV camera running at 30 FPS.

- TRIG_INTERVAL: 33.33 ms
- TRIG_POLARITY: 0, active low
- TRIG_ACT_TIME: 0.5 ms, leave default. The manual specifies it only has to be a
  minimum of 1 microsecond.
- TRIG_MODE: 1, because we want our camera driver to be ready to receive images
  before starting to trigger. This is essential to properly process sequence
  numbers.
- TRIG_PINS: 12, Leave default.

### Step 3

Wire up your cameras to your AUX port by connecting the ground and signal pins to
the appropriate place.

### Step 4

You will have to modify your driver to follow the sequence diagram above. Public
reference implementations for [IDS Imaging UEye](https://github.com/ProjectArtemis/ueye_cam)
cameras and for [IEEE1394 compliant](https://github.com/andre-nguyen/camera1394) cameras are available.
