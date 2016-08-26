# First App Tutorial (Hello Sky)
官网英文原文地址：http://dev.px4.io/tutorial-hello-sky.html

本教程详细解释了如何创建一个新的板载应用程序，以及如何运行它。

##前提条件

- Pixhawk 或者 Snapdragonhuozhehuozhe兼容的飞控
- PX4 工具链 [installed](../1_Getting-Started/install_toolchain.md)
- Github 账号  ([sign up for free](https://github.com/signup/free))

## 第一步：文件设置 

为了更方便的管理你的个人代码和上传更新到原始代码仓库，强烈建议使用fork命令通过GIT版本控制系统得到一个新的PX4固件。

-在github(https://github.com/signup/free) 上注册一个账户；
-登陆px4的固件托管网址(https://github.com/px4/Firmware/)，然后点击右上方的FORK按钮；
- If you are not already there, open the website of your fork and copy the private repository URL in the center.
- 点击到你fork后的站点，复制你的私人PX4代码仓库的URL地址。
- 克隆你的代码仓库到你的硬盘中，在命令行中输入：`git clone https://github.com/<youraccountname>/Firmware.git`。
  对于windows用户，请参考github帮助中的介绍(https://help.github.com/articles/set-up-git#platform-windows)。例如在github创建的官方应用程序中使用fork/clone命令克隆仓库到本地硬盘中。
- Update the git submodules: Run in your shell (on Windows in the PX4 console). 
- 更新px4代码包含的git子模块：运行命令行工具（在windows上运行PX4终端）
- 
<div class="host-code"></div>
```sh
cd Firmware
git submodule init
git submodule update --recursive
```

打开你本地硬盘克隆的软件仓库的`Firmware/src/examples/`文件夹，查看里面的文件。

## 第二步：创建小型应用程序

在`Firmware/src/examples/px4_simple_app`下，创建一个新的C语言文件`px4_simple_app.c`。(该文件已存在，你可以直接删掉它，来完全自主编辑学习。)

从默认头文件和main主函数开始，编辑这个文件。

<aside class="tip">
注意这个文件中的代码风格，所有PX4的软件版本都将遵守这个风格。
</aside>

```C
/****************************************************************************
 *
 *   Copyright (c) 2012-2015 PX4 Development Team. All rights reserved.
 *
 * Redistribution and use in source and binary forms, with or without
 * modification, are permitted provided that the following conditions
 * are met:
 *
 * 1. Redistributions of source code must retain the above copyright
 *    notice, this list of conditions and the following disclaimer.
 * 2. Redistributions in binary form must reproduce the above copyright
 *    notice, this list of conditions and the following disclaimer in
 *    the documentation and/or other materials provided with the
 *    distribution.
 * 3. Neither the name PX4 nor the names of its contributors may be
 *    used to endorse or promote products derived from this software
 *    without specific prior written permission.
 *
 * THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS
 * "AS IS" AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT
 * LIMITED TO, THE IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS
 * FOR A PARTICULAR PURPOSE ARE DISCLAIMED. IN NO EVENT SHALL THE
 * COPYRIGHT OWNER OR CONTRIBUTORS BE LIABLE FOR ANY DIRECT, INDIRECT,
 * INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES (INCLUDING,
 * BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES; LOSS
 * OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER CAUSED
 * AND ON ANY THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT
 * LIABILITY, OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN
 * ANY WAY OUT OF THE USE OF THIS SOFTWARE, EVEN IF ADVISED OF THE
 * POSSIBILITY OF SUCH DAMAGE.
 *
 ****************************************************************************/

/**
 * @file px4_simple_app.c
 * Minimal application example for PX4 autopilot
 *
 * @author Example User <mail@example.com>
 */

#include <px4_config.h>
#include <px4_tasks.h>
#include <px4_posix.h>
#include <unistd.h>
#include <stdio.h>
#include <poll.h>
#include <string.h>

#include <uORB/uORB.h>
#include <uORB/topics/sensor_combined.h>
#include <uORB/topics/vehicle_attitude.h>

__EXPORT int px4_simple_app_main(int argc, char *argv[]);

int px4_simple_app_main(int argc, char *argv[])
{
	PX4_INFO("Hello Sky!");
	return OK;
}
```

## 第三步：在NuttShell中注册应用程序，然后编译

现在应用程序已经完成并且能够运行，但还没有注册成NuttShell命令行工具。如果想要将应用程序编译到固件中，将它加到下面的模块编译列表中：
- Pixhawk v1/2: [Firmware/cmake/configs/nuttx_px4fmu-v2_default.cmake](https://github.com/PX4/Firmware/blob/master/cmake/configs/nuttx_px4fmu-v2_default.cmake)
- Pixracer: [Firmware/cmake/configs/nuttx_px4fmu-v4_default.cmake](https://github.com/PX4/Firmware/blob/master/cmake/configs/nuttx_px4fmu-v4_default.cmake)

在你的应用程序的下面的文件中添加一行：
  `examples/px4_simple_app`

编译它：

- Pixhawk v1/2: `make px4fmu-v2_default`
- Pixhawk v3: `make px4fmu-v4_default`

## 第四步：上传并且测试应用程序

使能uploader然后重置开发板：

- Pixhawk v1/2: `make px4fmu-v2_default upload`
- Pixhawk v3: `make px4fmu-v4_default upload`

在你重置开发板之前，会在后面打印如下的编译信息：

  `Loaded firmware for X,X, waiting for the bootloader...`

一旦开发板重置成功并且应用程序上传成功，打印信息：

<div class="host-code"></div>

```sh
Erase  : [====================] 100.0%
Program: [====================] 100.0%
Verify : [====================] 100.0%
Rebooting.

[100%] Built target upload
```

### 连接到终端

现在通过串口或USB连接到系统命令行终端(../12_Debugging-and-Advanced-Topics/advanced-system-console.md)。点击回车键能打开shell命令行工具：

```sh
  nsh>
```

输入“help”然后回车

```sh
  nsh> help
    help usage:  help [-v] [<cmd>]
  
    [           df          kill        mkfifo      ps          sleep       
    ?           echo        losetup     mkrd        pwd         test        
    cat         exec        ls          mh          rm          umount      
    cd          exit        mb          mount       rmdir       unset       
    cp          free        mkdir       mv          set         usleep      
    dd          help        mkfatfs     mw          sh          xd          
  
  Builtin Apps:
    reboot
    perf
    top
    ..
    px4_simple_app
    ..
    sercon
    serdis
```

现在‘px4_simple_app’已经是一个可用的命令了。输入`px4_simple_app`命令然后回车：

```sh
  nsh> px4_simple_app
  Hello Sky!
```

现在应用程序已经成功注册到系统中，能够被扩展并且运行一些有用的任务。

## Step 5: Subscribing Sensor Data

To to something useful, the application needs to subscribe inputs and publish outputs (e.g. motor or servo commands). Note that the *true* hardware abstraction of the PX4 platform comes into play here -- no need to interact in any way with sensor drivers and no need to update your app if the board or sensors are updated.

Individual message channels between applications are called *topics* in PX4. For this tutorial, we are interested in the [sensor_combined](https://github.com/PX4/Firmware/blob/master/src/modules/uORB/topics/sensor_combined.h) [topic](../6_Middleware-and-Architecture/uorb_messaging.md), which holds the synchronized sensor data of the complete system.

Subscribing to a topic is swift and clean:

```C++
#include <uORB/topics/sensor_combined.h>
..
int sensor_sub_fd = orb_subscribe(ORB_ID(sensor_combined));
```

The `sensor_sub_fd` is a topic handle and can be used to very efficiently perform a blocking wait for new data. The current thread goes to sleep and is woken up automatically by the scheduler once new data is available, not consuming any CPU cycles while waiting. To do this, we use the (poll())[http://pubs.opengroup.org/onlinepubs/007908799/xsh/poll.html] POSIX system call.

Adding `poll()` to the subscription looks like (*pseudocode, look for the full implementation below*):

```C++
#include <poll.h>
#include <uORB/topics/sensor_combined.h>
..
int sensor_sub_fd = orb_subscribe(ORB_ID(sensor_combined));

/* one could wait for multiple topics with this technique, just using one here */
px4_pollfd_struct_t fds[] = {
	{ .fd = sensor_sub_fd,   .events = POLLIN },
};

while (true) {
	/* wait for sensor update of 1 file descriptor for 1000 ms (1 second) */
	int poll_ret = px4_poll(fds, 1, 1000);
..
	if (fds[0].revents & POLLIN) {
		/* obtained data for the first file descriptor */
		struct sensor_combined_s raw;
		/* copy sensors raw data into local buffer */
		orb_copy(ORB_ID(sensor_combined), sensor_sub_fd, &raw);
		printf("[px4_simple_app] Accelerometer:\t%8.4f\t%8.4f\t%8.4f\n",
					(double)raw.accelerometer_m_s2[0],
					(double)raw.accelerometer_m_s2[1],
					(double)raw.accelerometer_m_s2[2]);
	}
}
```

Compile the app now by issuing

<div class="host-code"></div>

```
  make
```

### Testing the uORB Subscription

The final step is to start your application as background application.

```
  px4_simple_app &
```

Your app will flood the console with the current sensor values:

```
  [px4_simple_app] Accelerometer:   0.0483          0.0821          0.0332
  [px4_simple_app] Accelerometer:   0.0486          0.0820          0.0336
  [px4_simple_app] Accelerometer:   0.0487          0.0819          0.0327
  [px4_simple_app] Accelerometer:   0.0482          0.0818          0.0323
  [px4_simple_app] Accelerometer:   0.0482          0.0827          0.0331
  [px4_simple_app] Accelerometer:   0.0489          0.0804          0.0328
```

It will exit after printing five values. The next tutorial page will explain how to write a deamon which can be controlled from the commandline.

## Step 7: Publishing Data

To use the calculated outputs, the next step is to *publish* the results. If we use a topic from which we know that the ''mavlink'' app forwards it to the ground control station, we can even look at the results. Let's hijack the attitude topic for this purpose.

The interface is pretty simple: Initialize the struct of the topic to be published and advertise the topic:

```C
#include <uORB/topics/vehicle_attitude.h>
..
/* advertise attitude topic */
struct vehicle_attitude_s att;
memset(&att, 0, sizeof(att));
orb_advert_t att_pub_fd = orb_advertise(ORB_ID(vehicle_attitude), &att);
```

In the main loop, publish the information whenever its ready:

```C
orb_publish(ORB_ID(vehicle_attitude), att_pub_fd, &att);
```

The modified complete example code is now:

```C
#include <px4_config.h>
#include <px4_tasks.h>
#include <px4_posix.h>
#include <unistd.h>
#include <stdio.h>
#include <poll.h>
#include <string.h>

#include <uORB/uORB.h>
#include <uORB/topics/sensor_combined.h>
#include <uORB/topics/vehicle_attitude.h>

__EXPORT int px4_simple_app_main(int argc, char *argv[]);

int px4_simple_app_main(int argc, char *argv[])
{
	PX4_INFO("Hello Sky!");

	/* subscribe to sensor_combined topic */
	int sensor_sub_fd = orb_subscribe(ORB_ID(sensor_combined));
	orb_set_interval(sensor_sub_fd, 1000);

	/* advertise attitude topic */
	struct vehicle_attitude_s att;
	memset(&att, 0, sizeof(att));
	orb_advert_t att_pub = orb_advertise(ORB_ID(vehicle_attitude), &att);

	/* one could wait for multiple topics with this technique, just using one here */
	px4_pollfd_struct_t fds[] = {
		{ .fd = sensor_sub_fd,   .events = POLLIN },
		/* there could be more file descriptors here, in the form like:
		 * { .fd = other_sub_fd,   .events = POLLIN },
		 */
	};

	int error_counter = 0;

	for (int i = 0; i < 5; i++) {
		/* wait for sensor update of 1 file descriptor for 1000 ms (1 second) */
		int poll_ret = px4_poll(fds, 1, 1000);

		/* handle the poll result */
		if (poll_ret == 0) {
			/* this means none of our providers is giving us data */
			PX4_ERR("[px4_simple_app] Got no data within a second");

		} else if (poll_ret < 0) {
			/* this is seriously bad - should be an emergency */
			if (error_counter < 10 || error_counter % 50 == 0) {
				/* use a counter to prevent flooding (and slowing us down) */
				PX4_ERR("[px4_simple_app] ERROR return value from poll(): %d"
				       , poll_ret);
			}

			error_counter++;

		} else {

			if (fds[0].revents & POLLIN) {
				/* obtained data for the first file descriptor */
				struct sensor_combined_s raw;
				/* copy sensors raw data into local buffer */
				orb_copy(ORB_ID(sensor_combined), sensor_sub_fd, &raw);
				PX4_WARN("[px4_simple_app] Accelerometer:\t%8.4f\t%8.4f\t%8.4f",
				       (double)raw.accelerometer_m_s2[0],
				       (double)raw.accelerometer_m_s2[1],
				       (double)raw.accelerometer_m_s2[2]);

				/* set att and publish this information for other apps */
				att.roll = raw.accelerometer_m_s2[0];
				att.pitch = raw.accelerometer_m_s2[1];
				att.yaw = raw.accelerometer_m_s2[2];
				orb_publish(ORB_ID(vehicle_attitude), att_pub, &att);
			}

			/* there could be more file descriptors here, in the form like:
			 * if (fds[1..n].revents & POLLIN) {}
			 */
		}
	}
	PX4_INFO("exiting");

	return 0;
}
```

## Running the final example

And finally run your app:

```sh
  px4_simple_app
```

If you start QGroundControl, you can check the sensor values in the realtime plot (Tools -> Analyze)

## Wrap-Up

This tutorial covered everything needed to develop a "grown up" PX4 autopilot application. Keep in mind that the full list of uORB messages / topics is [available here](https://github.com/PX4/Firmware/tree/master/msg/) and that the headers are well documented and serve as reference.
