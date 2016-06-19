# PWM限制状态机


**[PWM_限制状态机] 控制PWM解锁前的输出以及解锁后的输入。**
*提供一个“解锁”及延迟*
Controls PWM outputs as a function of pre-armed and armed inputs. Provides a delay between assertion of "armed" and a ramp-up of throttle on assertion of the armed signal.
~~解锁信号~~
## Quick Summary

**Inputs**

- armed: asserted to enable dangerous behaviors such as spinning propellers
- pre-armed: asserted to enable benign behaviors such as moving control surfaces
  - this input overrides the current state
  - assertion of pre-armed immediately forces behavior of state ON, regardless of current state
    ** deassertion of pre-armed reverts behavior to current state

**States**

- INIT and OFF
  - pwm outputs set to disarmed values.
- RAMP
  - pwm ouputs ramp from disarmed values to min values.
- ON
  - pwm outputs set according to control values.

## State Transition Diagram

 ![pwm_limit_state_diagram](../pictures/diagrams\pwm_limit_state_diagram.png)
