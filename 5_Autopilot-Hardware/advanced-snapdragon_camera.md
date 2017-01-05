# Snapdragon: Camera and Optical Flow

官网英文原文地址：http://dev.px4.io/advanced-snapdragon_camera.html

Please follow the following instructions to use the camera and optical flow with a Snapdragon Flight.

# Snapdragon: Camera driver
This [package](https://github.com/PX4/snap_cam) provides tools to work with the Snapdragon Flight cameras as well as perform optical flow for use with the PX4 flight stack.
The package can be used with ROS by building with catkin or, alternatively, with pure cmake, where only the executables that do not depend on ROS will be built.

The package is to be compiled on the Snapdragon board. Two variants are provided: Building with ROS, where all features are available, and building with pure CMake, where only ROS-independent applications are compiled (including the optical flow node).

## Building with pure CMake
For the pure CMake install variant, clone the required repositories in a directory, e.g. `~/src`:
```sh
cd ~/src
git clone https://github.com/ethz-ait/klt_feature_tracker.git
git clone https://github.com/PX4/snap_cam.git
```

Initialize the Mavlink submodule:
```sh
cd snap_cam
git submodule init
git submodule update --recursive
```

Compile with:
```sh
mkdir -p build
cd build
cmake ..
make
```

Run the optical flow application with (note that you need to be root for this):
```sh
./optical_flow [arguments ...]
```

## Building with ROS
### Prerequisites
To run the ROS nodes on the Snapdragon Flight, ROS indigo has to be installed. Follow [this](http://wiki.ros.org/indigo/Installation/UbuntuARM) link to install it on your Snapdragon Flight. (preferably using the linaro user: `$ su linaro`)

If you're having permission issues while installing ros try
```sh
sudo chown -R linaro:linaro /home/linaro
```

#### Install the following dependencies:
ROS dependencies
```sh
sudo apt-get install ros-indigo-mavlink ros-indigo-tf ros-indigo-orocos-toolchain ros-indigo-angles ros-indigo-tf2 ros-indigo-tf2-ros
```

Others
```sh
sudo apt-get install libeigen3-dev sip-dev libyaml-cpp-dev
```

To install OpenCV, [download](http://px4-tools.s3.amazonaws.com/opencv3_20160222-1_armhf.deb) and push the `.deb` package to the Snapdragon and install it using

<div class="host-code"></div>
```sh
adb push /path/to/file /home/linaro/
dpkg -i opencv3_20160222-1_armhf.deb
```
or use
```sh
sudo apt-get install ros-indigo-opencv3
```

#### create a catkin workspace
Next, create a catkin workspace (e.g. in /home/linaro)
```sh
mkdir -p ~/catkin_ws/src
cd ~/catkin_ws/src
catkin_init_workspace
cd ..
catkin_make
```

Then clone the following four catkin packages and build
```sh
cd src
git clone https://github.com/ros-perception/vision_opencv
git clone https://github.com/ros-perception/image_common
git clone https://github.com/ethz-ait/klt_feature_tracker.git
git clone https://github.com/PX4/snap_cam.git
cd ..
catkin_make
```

## Image publisher node
Once your catkin workspace is built and sourced you can start the image publisher using
```sh
roslaunch snap_cam <CAM>.launch
```
where `<CAM>` is either `optflow` or `highres` to stream the optical flow or high resolution cameras, respectively.
You can set the parameters (camera, resolution and fps) in the launch files (`pathToYourCatkinWs/src/snap_cam/launch/<cam>.launch`)

You can now subscribe to the images in your own ROS node.

## Camera calibration
For optical flow computations, a calibration file needs to be used. This package contains default calibration files for VGA and QVGA resolution. Nevertheless, we recommend calibrating your camera (see below) for better performance.
For this you must build this package with catkin as described above and launch the optical flow image publisher:
```sh
roslaunch snap_cam optflow.launch
```

Clone and build this package in a catkin workspace on your computer. Add any missing dependencies:
```sh
sudo apt-get install python-pyside
```
On your computer launch the calibration app:
```sh
export ROS_MASTER_URI=http://<snapdragon IP>:11311
roslaunch snap_cam cameraCalibrator.launch
```

NOTE:
If your image topics are empty, make sure to set the environment variable ROS_IP to the respective IP on both devices.


Set the appropriate checkerboard parameters in the app.
Start recording by clicking on the button and record your checkerboard from sufficiently varying angles.
Once done, click stop recording.
The camera calibration will be written to `pathToYourCatkinWs/src/snap_cam/calib/cameraParameters.yaml`.
Push this file to your snapdragon.
```sh
adb push /pathToYourCatkinWs/src/snap_cam/calib/cameraParameters.yaml pathToSnapCam/calib/cameraParameters.yaml
```

## Running the optical flow
### With pure CMake
Run the following in your build directory:
```sh
./optical_flow [arguments ...]
```
All arguments are optional.
* `-r` specifies the camera resolution. The default is `VGA`. Valid resolutions are `VGA` and `QVGA`.
* `-f` specifies the camera frame-rate. The default is 15. Valid values are 15, 24, 30, 60, 90, 120.
* `-n` specifies the number of features with which to compute the optical flow. The default is 10.
* `-c` specifies the calibration file. The default is `../calib/<resolution>/cameraParameters.yaml`.

### With ROS
After sourcing your workspace with `source ~/catkin_ws/devel/setup.bash`, run:
```sh
rosrun snap_cam optical_flow [arguments ...]
```
The arguments are the same as for the pure CMake build/=.
