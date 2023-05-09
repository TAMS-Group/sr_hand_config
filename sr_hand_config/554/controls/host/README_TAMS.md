We deleted all controller configurations which we do not use.

In order to switch between the PWM-based position controller and the
torque-based effort controller *fast* (and without the whole
reload-everything-after-this-roslaunch-to-upload-new-yaml-parameters
procedure Shadow provides for it), the parameters for both controllers
need to be loaded on the parameter server at the same time with different names.
Sadly, the current parameter layout by Shadow reuses the `*_position_controller`
and `*_effort_controller` names (instead of including a `_pwm` and `_torque` tag)
and overrides them with different values when switching between the hardware
communication modes (PWM or FORCE).

Side-note: Ideally, you would not even switch the mode manually with a service,
but decide which mode to use in the hardware interface based on the set of
controllers that are activated.

To work around this problem, we simplify the config files here to *one* set
of two files stored in the `pwm` folder to not break Shadows roslaunch scripts.

However, the files describe
1. a position controller to be used in PWM control mode; SrhJointPositionController:
   a PID controller with some logic for backlash compensation
and
2. an effort controller to be used in torque mode mode; SrhEffortJointController:
   it just forwards the commanded efforts to the faster update loop in the palm firmware
