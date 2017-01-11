# PWM_limit状态机

官网英文原文地址：http://dev.px4.io/concept-pwm_limit.html

PWM_limit 状态机根据解锁前和解锁后的输入控制PWM输出，并提供'armed'置1和解锁信号生效之间的延迟来延缓油门上升。

## 快速概要

**输入**

* armed: 置1使能诸如旋转螺旋桨的危险行为。
* pre-armed: 置1使能诸如移动控制面的良性行为。
  * 这个输入覆盖当前状态。
  * pre-aremd置1无视当前状态，立即强制转移到状态ON，值0则回复到当前状态。


**状态**

* INIT和OFF
  * pwm输出值设定为未解锁值。

* RAMP
  * pwm输出值从未解锁值上升到最小值。

* ON
  * pwm输出值根据控制量设定。


## 状态转移图

![pwm_limit_state_diagram](../pictures/diagrams/pwm_limit_state_diagram.png)

