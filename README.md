# CNC_Plotter(Ongoing)

Project purpose is changed due to case of ink printer. It is not suitable for this job. When the spindle motor works the hole case vibrates to much to engrave PCB. The project will be revised and will be become CNC_plotter with the same equipment.

Simplest DIY CNC plotter. The base of project uses grblHAL and the system is built on ink printer case. The system works with master and slave stm32 controller for open and closed loop motor control mechaniscms.

Z_axis:
  Z axis is custom made 3d printed linear guide rail. Nema17 is used to control and A4988 step driver is used. The Z_Axis is controlled by master stm32.

X&Y_Axis:
  These two axis are the printers local axis. They converted to a closed loop system. The system uses printers encoders sensors. The close loop is controlled by slave stm32. Slave taking the step, dir datas from master(grblHAL) then drives two DC(rs445) motor with two BTS7960 motor driver. It comapares the steps of master and encoder strips then drives motor. Also, if the steps missed it gives error.

Power Supply:  
  The power supply is a desktop computer PSU. For the logic level type-c connection is used. All stm32 and logic level works with 3.3 V. The 12V comes from the PSU and feeds the motors. All ground points are connected. 

All the other features are supported from grblHAL, X,Y and Z calibration works. Z mapping works.

Only the axis are tested and calibrated. Project is ongoing.
