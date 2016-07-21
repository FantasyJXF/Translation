# **Optical Flow on the Snapdragon Flight**

The Snapdragon Flight board has a downward facing gray-scale camera which can be used for optical flow based position stabilization.

Besides a camera, optical flow requires a downward facing distance sensor. Here, the use of the TeraRanger One is discussed.

## **TeraRanger One setup**

To connect the TeraRanger One \(TROne\) to the Snapdragon Flight, the TROne I2C adapter must be used. The TROne must be flashed with the I2C firmware by the vendor.

The TROne is connected to the Snapdragon Flight through a custom DF13 4-to-6 pin cable. The wiring is as follows:



