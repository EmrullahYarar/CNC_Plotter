# CNC_Plotter(Ongoing)

Project purpose is changed due to case of ink printer. It is not suitable for this job. When the spindle motor works the hole case vibrates to much to engrave PCB. The project will be revised and will be become CNC_plotter with the same equipment.

Simplest DIY CNC engraver for double sided PCBs. The base of project uses grblHAL and the system is built on ink printer case. The system works with master and slave stm32 controller for open and closed loop motor control mechaniscms.

Z_axis:
  Z axis is custom made 3d printed linear guide rail. Nema17 is used to control and A4988 step driver is used. The Z_Axis is controlled by master stm32.

X&Y_Axis:
  These two axis are the printers local axis. They converted to a close loop system. The system uses printers encoders sensors. The close loop is controlled by slave stm32. Slave taking the step, dir datas from master(grblHAL) then drives two DC(rs445) motor with two BTS7960 motor driver. It comapares the steps of master and encoder strips then drives motor. Also, if the steps missed it gives error.

Spindle:
  The spindle motor is 1/10 rc car 4 pole dc 3650mm 2300kv motor. It driven with a ESC by the master stm32. The arming is done from the gSender program. The speed can be adjusted. For engraving ER8 and V-bits are used.

Power Supply:  
  The power supply is a desktop computer PSU. For the logic level type-c connection is used. All stm32 and logic level works with 3.3 V. The 12V comes from the PSU and feeds the 3 axis motors. All ground points are connected. 

All the other features are supported from grblHAL, X,Y and Z calibration works. Z mapping works.

Only the axis are tested and calibrated. Project is ongoing.

## System Architecture (Hardware & Signal Flow)

```mermaid
graph TD
    %% Ana Kontrolcü
    PC["💻 Computer / gSender"] -- USB --> Master

    %% Master Board
    subgraph Master_Control [Master Controller]
        Master{"Master Board<br/>(grblHAL - STM32)"}
    end

    %% Z Ekseni
    subgraph Z_Axis [Z Axis - Open Loop]
        A4988["A4988 Driver<br/>(vRef: 1.5A)"]
        NEMA17["17HS4401 NEMA 17<br/>(Stepper)"]
        Master -- "Step/Dir" --> A4988
        A4988 -- "4 Wires (Bipolar)" --> NEMA17
    end

    %% İş Mili (Spindle)
    subgraph Spindle_System [Spindle System - BLDC]
        ESC["40A Yellow ESC<br/>(Red Pin Floating)"]
        BLDC["Brushless Motor<br/>(Spindle)"]
        Master -- "PWM (PA8)" --> ESC
        ESC -- "3 Phase Wires" --> BLDC
    end

    %% X ve Y Kapalı Çevrim (Servo) Sistemi
    subgraph XY_Axis [X & Y Axes - Closed Loop Servo]
        Slave{"Slave Board<br/>(STM32F401)"}
        BTS7960["BTS7960B Drivers<br/>(43A)"]
        DCMotor["Brushed DC Motors"]
        Encoder["Optical Encoders"]
        
        Master -- "Step/Dir" --> Slave
        Slave -- "Alarm / E-Stop" --> Master
        
        Slave -- "PWM & Enable" --> BTS7960
        BTS7960 -- "High Current" --> DCMotor
        DCMotor -. "Mechanical Link" .-> Encoder
        Encoder -- "Sensor Data" --> Slave
    end

    %% Topraklama (Statik)
    Earth(("220V Earth<br/>(Star Grounding)"))
    BLDC -. "Chassis Ground" .-> Earth
