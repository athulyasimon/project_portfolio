---
layout: project
title: Interfacing a Camera and Microcontroller
date: December 12, 2014
image: msr-student-template-favicon.png
---

## Overview
The goal of this project was to interface the OV7670 camera with the PIC32MX795F512L microcontroller. 

##Microcontroller
The PIC32 was previously used in our Mechatronics classes and was sufficient for this project. The PIC32 is housed by a [NU32 development board](http://hades.mech.northwestern.edu/index.php/NU32:_Introduction_to_the_PIC32). This board also has a serial-to-USB converter which is useful for transmitting data from the camera to my computer. The camera communicates using SCCB (Serial Camera Control Bus). SCCB protocol is similar to I2C protocol and therefore I2C communication is used between the microcontroller and the camera. An I2C library was provided by Nick Marchuk for use in out Mechatronics class. The library as well as other downloads helpful for setting up the microcontroller with a computer can be found [here](http://hades.mech.northwestern.edu/index.php/NU32_Software) in the Full Book Source Code download. 

##Camera
The OV7670 with FIFO is a cheap CMOS camera. It requires a 3V power supply and is capable of taking 640x480 resolution pictures at up to 30 frames per second. 

#Camera Version

There are many available versions of the OV7670 camera. Some come with an additional AL422 FIFO chip which is helpful for image storage. With the FIFO chip the image can be stored on the chip and then read out later. Otherwise the image needs to be read out at the same time as it is being captured. There are two different versions available of the OV7670 cameras with the AL422 FIFO chip. The version used in this project is the 22 pin version 
![pin diagram](add image link)

PIN Diagram – explanation of pins



Frame resolution – registers to change

Timing Diagram

Outline of Code

#Frame resolution

The OV7670 is capable of outputting images with various frame resolutions. 
* 
*
*
*

#Color Format


##Matlab
uart
Reading in image
yuv to rgb conversion


The current code can be seen on my [GitHub page](https://github.com/athulyasimon/ov7670_with_PIC32).










[OV7670 Datasheet](http://www.voti.nl/docs/OV7670.pdf)
[PIC32MX795F512L Datasheet](



