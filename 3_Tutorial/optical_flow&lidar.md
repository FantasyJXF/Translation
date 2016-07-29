# Optical flow and LIDAR-Lite

官网英文原文地址：http://dev.px4.io/flow_lidar_setup.html

This page shows you how to set up the PX4Flow and a LIDAR-Lite for position estimation in the INAV position estimator. A short video demonstrating a position hold can be seen here:

* [indoor](https://www.youtube.com/watch?v=MtmWYCEEmS8) 
* [outdoor](https://www.youtube.com/watch?v=4MEEeTQiWrQ)

  ![px4flow](../pictures/hardware\px4flow.png)
  ![lidarlite](../pictures/hardware\lidarlite.png)


## Hardware

The PX4Flow has to point towards the ground and can be connected using the I2C port on the pixhawk.
For the connection of the LIDAR-Lite please refer to [this](https://pixhawk.org/peripherals/rangefinder?s[]=lidar) page.
For best performance make sure the PX4Flow is attached at a good position and is not exposed to vibration. \(preferably on the down side of the quad-rotor\).

![flow_lidar_attached](../pictures/hardware\flow_lidar_attached.jpg)

## Parameters

All the parameters can be changed in QGroundControl

* SENS\_EN\_LL40LS
  Set to 1 to enable lidar-lite distance measurements

* INAV\_LIDAR\_EST
  Set to 1 to enable altitude estimation based on distance measurements

* INAV\_FLOW\_DIST\_X and INAV\_FLOW\_DIST\_Y
  These two values \(in meters\) are used for yaw compensation.
  The offset has to be measured according to the following figure:
   ![flowing](../pictures/hardware\px4flow_offset.png)

* ​

  In the above example the offset of the PX4Flow \(red dot\) would have a negative X offset and a negative Y offset.

* INAV\_LIDAR\_OFF
  Set a calibration offset for the lidar-lite in meters. The value will be added to the measured distance.


## Advanced

For advanced usage\/development the following parameters can be changed as well. Do NOT change them if you do not know what you are doing!

* INAV\_FLOW\_W
  Sets the weight for the flow estimation\/update
* INAV\_LIDAR\_ERR
  Sets the threshold for altitude estimation\/update in meters. If the correction term is bigger than this value, it will not be used for the update.

