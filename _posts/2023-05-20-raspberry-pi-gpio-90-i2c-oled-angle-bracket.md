---
title: GPIO 90 Degree i2c OLED Angle Bracket
layout: post
carousels:
  - images:
    - image: /images/bracket/bracket2.jpg
    - image: /images/bracket/bracket3.jpg
    - image: /images/bracket/bracket4.png

---

A Raspberry Pi GPIO compatible multiplatform 90° OLED bracket for i2c OLED Displays.

{% include image.html
            img="/images/bracket/bracket1.png"
            title="limcluster_build1"
            caption="Fig 1: 3D Render of the Raspberry Pi GPIO compatible multiplatform 90° OLED anlge bracket" %}

The angle bracket allows to easily mount a i2c OLED display onto a Raspberry Pi compatible GPIO. It has been tested with the following boards:

- Raspberry Pi 4B
- Raspberry Pi Compute Module 4 with
   - [Waveshare - Mini Base Board (A) Designed for Raspberry Pi Compute Module 4 (CM4-IO-BASE-A)](https://amzn.to/3MqSDPz)
   - [Waveshare - Raspberry Pi Compute Module 4 IO Board With PoE Feature, for all Variants of CM4 (18606)](https://amzn.to/3OBno6K)
   - [DFRobot - PiTray mini for Raspberry Pi Compute Module 4](https://bit.ly/42XD09a)
   - [Raspberry Pi - Compute Module 4 IO Board](https://amzn.to/3MrqpDY)
- [UP Board - UP4000](https://up-board.org/up-4000/)
- [Banana Pi - BPI-CM4 computer module and development base board](https://amzn.to/42RHxKc) and [Banana Pi BPI-CM4 - Banana Pi Wiki](https://wiki.banana-pi.org/Banana_Pi_BPI-CM4)

The 90° angle makes it easy to connect

- Ground (GND)  - GPIO pin 6
- VCC (3V Power) - GPIO pin 1
- SCL1 I2C- GPIO pin 5
- SDA1 I2C  - GPIO pin 3

of a compatible [128x32 OLED display](https://amzn.to/41TV1D) with the following pin order (top to bottom):

- SDA
- SCL
- VCC
- GND

{% include carousel.html height="75" unit="%" duration="7" number="1" %}

<p style="font:Arial;text-align:center;margin-top: -20px; font-weight: bold; font-size: 14px">

Collage 0: Various Pictures of the Raspberry Pi GPIO compatible multiplatform 90° angle I2C OLED bracket

</p>
