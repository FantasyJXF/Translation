# 综合测试

官网英文原文地址：http://dev.px4.io/tutorial-integration-testing.html

这是综合测试，测试会自动执行([Jenkins CI](../12_Debugging-and-Advanced-Topics/advanced-jenkins-ci.md))。

## ROS / MAVROS测试

前提:

- [SITL仿真](../4_Simulation/basic_simulation.md)
- [Gazebo](../4_Simulation/gazebo_simulation.md)
- [ROS and MAVROS](../4_Simulation/interfacingto_ros.md)

### 执行测试

运行完整的MAVROS测试套件：

```sh
cd <Firmware_clone>
source integrationtests/setup_gazebo_ros.bash $(pwd)
rostest px4 mavros_posix_tests_iris.launch
```

或者使用GUI查看：

```sh
rostest px4 mavros_posix_tests_iris.launch gui:=true headless:=false
```

### 编写新的MAVROS测试 (Python)

<aside class="note">
目前处于早期阶段，采用了很多简化测试(helper classes/methods etc.)。
</aside>

#### 1.) 创建新的测试脚本

测试脚本位于`integrationtests/python_src/px4_it/mavros/`，可以参考这些脚本文件。或者查阅ROS官方文档学习如何使用[unittest](http://wiki.ros.org/unittest)。

空的测试框架:

```python
#!/usr/bin/env python
# [... LICENSE ...]

#
# @author Example Author <author@example.com>
#
PKG = 'px4'

import unittest
import rospy
import rosbag

from sensor_msgs.msg import NavSatFix

class MavrosNewTest(unittest.TestCase):
    """
    Test description
    """

    def setUp(self):
        rospy.init_node('test_node', anonymous=True)
        rospy.wait_for_service('mavros/cmd/arming', 30)

        rospy.Subscriber("mavros/global_position/global", NavSatFix, self.global_position_callback)
        self.rate = rospy.Rate(10) # 10hz
        self.has_global_pos = False

    def tearDown(self):
        pass

    #
    # General callback functions used in tests
    #
    def global_position_callback(self, data):
        self.has_global_pos = True

    def test_method(self):
        """Test method description"""

        # FIXME: hack to wait for simulation to be ready
        while not self.has_global_pos:
            self.rate.sleep()

        # TODO: execute test

if __name__ == '__main__':
    import rostest
    rostest.rosrun(PKG, 'mavros_new_test', MavrosNewTest)
```

#### 2.) 只运行新的测试

```sh
# Start simulation
cd <Firmware_clone>
source integrationtests/setup_gazebo_ros.bash $(pwd)
roslaunch px4 mavros_posix_sitl.launch

# Run test (in a new shell):
cd <Firmware_clone>
source integrationtests/setup_gazebo_ros.bash $(pwd)
rosrun px4 mavros_new_test.py
```

#### 3.) 添加新的测试结点到launch文件

在`launch/mavros_posix_tests_irisl.launch`中的测试组中添加新的条目：

```xml
	<group ns="$(arg ns)">
		[...]
        <test test-name="mavros_new_test" pkg="px4" type="mavros_new_test.py" />
    </group>
```

按照前文所述方法运行完整测试套件。

