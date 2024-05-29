# Advanced Easy to use Burgler Alarm
## Introduction
In the age of modern IoT devices, CCTV cameras are commonly used for surveillance. However, they are often difficult to install, require internet connectivity, consume significant memory, and can't be installed in private rooms where privacy is a concern. Therefore, there is a need for a medium-level security device that can detect trespassing, is easy to install, and operates with minimal power.

## Overview
The Advanced Easy to Use Burglar Alarm uses an ultrasonic radar sensor to detect any object passing through its field of vision. It is equipped with a passive buzzer that alerts the user whenever an intrusion is detected. While similar functionality can be achieved with a laser detection system, where a laser is pointed at a Light Dependent Resistor (LDR) and detects trespassing when its line of sight is blocked, such systems require extensive setup and wiring and are not foolproof.

In contrast, the Advanced Easy to Use Burglar Alarm is designed for easy installation. It only needs to be placed perpendicular to a solid surface. One of the key features of this device is its adaptability through the auto-adjust feature. When placed within 0.1 - 4 meters from a solid surface and turned on, the device's LED lights up for around 10 seconds, during which it measures the distance to the solid surface and sets its threshold. After the LED turns off, the device is ready to detect any object passing through its field of view and alerts the user with its buzzer, and just requires 5v DC power which can be provided with a 5v DC adapter or a battery bank.

## Key Features
- Easy Installation: Requires minimal setup; simply place it perpendicular to a solid surface, with 5v DC connection.
- Auto-Adjust Feature: Automatically calibrates the detection threshold within 10 seconds of being turned on.
- Adaptable Range: Can be placed between 0.1- 4 meters from the detection surface.
- Low Power Consumption: Designed to operate efficiently with just 5v DC power which can be provided from a 5v adapter or a Battery bank.
- Privacy-Friendly: Suitable for use in private rooms without violating privacy.

## Components Required
- VSD Squadron Mini developement board
- Male USE C Cable
- HC-SR04 Ultrasonic Sensor
- Bread Board
- Male to Male; Male to Female jumper cable
- Red LED
- Passive Buzzer
- 220 Ohm Resistor


## Circute Diagram
<img width="421" alt="Circute diag" src="https://github.com/Nahusha-Karnam/Entenders/assets/171113429/01651e4e-066b-4697-9705-131a6dbaddcb">


## Table for Pin Connection
| HC-SR04 Ultrasonic Sensor | VSD Squadron Mini |
|---------------------------|-------------------|
| VCC | 5V |
| Trig | PD2 |
| Echo | PD1 |
| Gnd | Gnd |



| LED | Resistor | VSD Squadron Mini|
|-----|------|------------------|
| + |  | PD4 |
| - | 220Ω | Gnd|


| Buzzer | Resistor | VSD Squadron Mini|
|--------|----------|------------------|
| Pin 1 |  | PD3 |
| Pin 2 | 220Ω | Gnd|

| Toggle Switch |  VSD Squadron Mini |
|---------------|--------------------|
| Pin 1 | 5V |
| Comm | PD5 |

