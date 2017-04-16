# 预起飞传感器和拓展卡尔曼滤波检查(Preflight Sensor and EKF Checks)

官网英文原文地址：http://dev.px4.io/pre_flight_checks.html

这个控制模块会执行多个由COM_ARM<>参数控制的预起飞传感器特性与EKF检测. 如果这些检测失败, 电机会被禁止解锁而且会产生以下错误信息:

* PREFLIGHT FAIL(预起飞失败): EKF HGT ERROR
  * 当IMU和高度检测数据不一致时,会产生此错误.
  * 校准加速度计和陀螺仪后重启飞行器, 如果这个错误任然存在,检查高度传感器存在的问题.
  * 检测由参数COM_ARM_EKF_HGT控制.


* PREFLIGHT FAIL: EKF VEL ERROR
  * 当IMU和GPS速度检测数据不一致时,会产生此错误.
  * 检查GPS速度数据是否存在不切合实际的数据跳跃,如果GPS品质良好，执行加速机和陀螺仪校准，并重新启动飞行器。
  * 检测由参数COM_ARM_EKF_VEL控制.


* PREFLIGHT FAIL: EKF HORIZ POS ERROR
  * 当IMU和位置测量数据（GPS或外部视觉）不一致时，会产生此错误.
  * 检查位置传感器数据是否存在不切实际的数据跳转。如果数据品质良好，执行加速和陀螺仪校准，并重新启动飞行器。
  * 此检测由参数COM_ARM_EKF_POS控制。


* PREFLIGHT FAIL: EKF YAW ERROR
  * 当使用陀螺仪数据估计的偏航角和来自磁力计或外部视觉系统的偏航角不一致时，产生该误差。
  * 检查IMU数据是否存在大偏航率的偏移量，并检查磁力计调整和校准。
  * 此检测由参数COM\_ARM\_EKF\_POS控制.


* PREFLIGHT FAIL: EKF HIGH IMU ACCEL BIAS
  * 当由EKF估计的IMU加速度计偏差过大时，该误差产生.
  * 此检测由参数COM\_ARM\_EKF\_AB控制.


* PREFLIGHT FAIL: EKF HIGH IMU GYRO BIAS
  *当由EKF估计的IMU陀螺仪偏差过大时,产生该误差.
  *此检测由参数COM\_ARM\_EKF\_GB控制.


* PREFLIGHT FAIL: ACCEL SENSORS INCONSISTENT - CHECK CALIBRATION
  * 当来自不同IMU单元的加速度测量值不一致时，会产生此错误信息.
  * 此检测仅适用于具有多个IMU的电路板.
  * 此检测由参数COM\_ARM\_IMU\_ACC控制.


* PREFLIGHT FAIL: GYRO SENSORS INCONSISTENT - CHECK CALIBRATION
  * 当来自不同IMU单元的角速率测量不一致时，会产生此错误信息.
  * 此检查仅适用于具有多个IMU的电路板.
  * 此检测由参数COM\_ARM\_IMU\_GYR控制.


## COM\_ARM\_WO\_GPS
参数COM\_ARM\_WO\_GPS决定是否允许在没有GPS信号的情况下解锁。当不存在GPS信号时，该参数必须设置为0才允许解锁。只有选择的飞行模式不需要GPS，才允许在无GPS的情况下解锁.

## COM\_ARM\_EKF\_POS

参数COM_ARM_EKF_POS决定EKF惯性测量和位置参考（GPS或外部视觉）之间的最大准许不一致值。默认值为0.5时,允许差异不超过EKF最大容忍值的50％，并在飞行开始时提供误差增加的一定范围.

## COM\_ARM\_EKF\_VEL

参数COM\_ARM\_EKF\_VEL决定EKF惯性测量和GPS速度测量之间的最大准许不一致。默认值为0.5时,允许差异不超过EKF最大容忍值的50％，并在飞行开始时提供误差增加的一定范围.

## COM\_ARM\_EKF\_H

参数COM_ARM_EKF_HGT决定EKF惯性测量和高度测量（Baro，GPS，测距仪或外部视觉）之间的最大准许不一致值。默认值为0.5允许差异不超过EKF最大容忍值的50％，并在飞行开始时提供误差增加的一定范围.

## COM\_ARM\_EKF\_YAW

参数COM_ARM_EKF_YAW决定EKF惯性测量和偏航测量（磁力计或外部视觉）之间允许的最大准许不一致值。默认值为0.5允许差异不超过EKF最大容忍值的50％，并在飞行开始时提供误差增加的一定范围。

## COM\_ARM\_EKF\_AB

参数COM_ARM_EKF_AB决定EKF估计的最大准许IMU加速度计偏差。默认值0.005允许高达0.5 m / s / s的加速度偏差。

## COM\_ARM\_EKF\_GB

参数COM_ARM_EKF_GB决定EKF估计的最大准许IMU陀螺仪偏差.默认值0.00087允许高达5度/秒的开启陀螺仪偏差。

## COM\_ARM\_IMU\_ACC

参数COM_ARM_IMU_ACC决定用于飞行控制的默认IMU与其他IMU单元（如果适用）之间的加速度测量中允许的最大准许不一致值。

## COM\_ARM\_IMU\_GYR

参数COM_ARM_IMU_GYR决定用于飞行控制的默认IMU和其他IMU单元（如果适用）之间的角速率测量中允许的最大准许不一致值。
