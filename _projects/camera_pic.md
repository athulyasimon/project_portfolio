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

####Camera Version

There are many available versions of the OV7670 camera. Some come with an additional AL422 FIFO chip which is helpful for image storage. With the FIFO chip the image can be stored on the chip and then read out later. Otherwise the image needs to be read out at the same time as it is being captured. There are two different versions available of the OV7670 cameras with the AL422 FIFO chip. The version used in this project is the 22 pin version 

![](https://raw.githubusercontent.com/athulyasimon/project_portfolio/gh-pages/public/images/ov7670_project/fifo_ov7670_med.png)

####PIN Explanation

 | |
---|---
VCC - 3.3V Input power supply|GND - Ground Input
SIOC - Used for I2C, connects to SCL pin on microcontroller|SIOD - Used for I2C, connects to SDA pin on microcontroller
VSYNC - Output that is high while a new frame is being output |HREF - Output that is high while a new row is being output
D7-D0 - Image data is output on these pins one byte at a time|
RST - Input to reset the camera|PWDN - Input to put the camera into power down mode
STR - Camera strobe output that can be used to turn on an LED light while the camera takes an image|RCK - Input for clock pulse, each clock cycle corresponds to a new byte
WR - Set high to enable the writing of data to the camera's frame buffer memory|OE(Output Enable) - Set high for valid camera output from the D7-D0 pins
WRST(Write reset) - Input pin that resets the frame buffer memory's write pointer|RRST(Read reset) - Input pin that resets the frame buffer memory's read pointer

Replace with table image

####Timing Diagrams
Getting images properly from the camera entirely depends on timing. The following diagrams were taken from [Beginning Arduino ov7670 Camera Development](http://www.amazon.com/dp/B010Y37XQG/?tag=stackoverfl08-20). Although the book is written for interfacing the camera with an Arduino microcontroller, it was very helpful in explaining the timing diagrams and giving an outline for the code. 

Here is a simple description of what is shown below. VSync will go high at the start of a new frame, then HREF will go high for the start of every row and the pixels for each row will be output from the D7-D0 pins every time PCLK is high. A second high VSync signals the end of a new frame. Parts of the code are explained in the next section and the entire code can be found [here](https://github.com/athulyasimon/ov7670_with_PIC32/blob/master/main.c).

![VGA Frame Timing](https://raw.githubusercontent.com/athulyasimon/project_portfolio/blob/gh-pages/public/images/ov7670_project/VGA%20Frame%20Timing.png)
![Horizontal Timing](https://raw.githubusercontent.com/athulyasimon/project_portfolio/gh-pages/public/images/ov7670_project/Horizontal%20Timing.png)


####Steps to Capture Image into Camera's Frame Buffer Memory
1. [Identify when VSync is high](https://github.com/athulyasimon/ov7670_with_PIC32/blob/5ca605fe3d894c1da259ed6ebd53389eb1c3dc2d/main.c#L67) - I used a change notification pin to trigger when VSync goes high. The initialization of this pin can be found [here](https://github.com/athulyasimon/ov7670_with_PIC32/blob/5ca605fe3d894c1da259ed6ebd53389eb1c3dc2d/main.c#L336-L343)
 
```
void __ISR(_CHANGE_NOTICE_VECTOR, IPL3SOFT) VSyncInterrupt(void) { // INT step 1
	newF = PORTF; // since pins on port F are being monitored by CN,
				  // must read both to allow continued functioning

	read_state = !read_state; //first VSync is beginning of frame 
								// and second Vsync is end of frame

	if(read_state){
		reset_write_pointer();
		FIFO_write_enable();
	}

	if(!read_state){
		FIFO_write_disable();
		CNCONbits.ON = 0; // Temporarily turn CN pin off - this is used while debugging
		read_image();
		display_image();
	}
	
	IFS1bits.CNIF = 0; // clear the interrupt flag
}
```

2. [Reset Write Pointer](https://github.com/athulyasimon/ov7670_with_PIC32/blob/5ca605fe3d894c1da259ed6ebd53389eb1c3dc2d/main.c#L345-L350) - Pin E9 (defined as WRST) is used to reset the write pointer so that the image starts saving from the beginning of the frame. The pointer is set low to reset then returned to its original high signal. 

```
void reset_write_pointer(){
	//Reset Write Pointer to 0 which is the beginning of the frame
	//default is high, set pin low to reset
	LATECLR = WRST; //set pin low
	LATESET = WRST; //set pin high
}
```

3. [FIFO Write Enable](https://github.com/athulyasimon/ov7670_with_PIC32/blob/5ca605fe3d894c1da259ed6ebd53389eb1c3dc2d/main.c#L352-L355) - Pin E8 (defined as WR) is set high to enable the writing of the image to ram

```
void FIFO_write_enable(){
	//Set FIFO write enable to active (high) so that image can be written to ram
	LATESET = WR;	
}
```

4. [Identify when VSYNC is high again](https://github.com/athulyasimon/ov7670_with_PIC32/blob/5ca605fe3d894c1da259ed6ebd53389eb1c3dc2d/main.c#L71) - The change notification pin just identifies when VSync is high. In order to determine if this is the first trigger or the second trigger I have a read_state variable that will switch back and forth. The memory can only store one image at a time so once the image is read into memory the change notification pin is turned off. Once the image has been displayed the read state variable and the change notification pin can be reset to capture a new image. 
```
	read_state = !read_state; //first VSync is beginning of frame 
```

5. [FIFO Write Disable](https://github.com/athulyasimon/ov7670_with_PIC32/blob/5ca605fe3d894c1da259ed6ebd53389eb1c3dc2d/main.c#L357-L360) - Pin E8 (defined as WR) is set low to enable the writing of the image to ram
```
void FIFO_write_disable(){
	//Set FIFO write enable to inactive (low) so that image cannot be written to ram
	LATECLR = WR;	
}
```
 
####Steps to Retrieve the Image from the Camera's Frame Buffer Memory
1. [Reset Read Pointer](https://github.com/athulyasimon/ov7670_with_PIC32/blob/5ca605fe3d894c1da259ed6ebd53389eb1c3dc2d/main.c#L404-L409) - Pin F0 (defined as RRST) is used to reset the read pointer so that the image starts being output from the beginning of the frame. The pointer is set low to reset then returned to its original high signal. 
```
void reset_read_pointer(){
	//Set the FIFO read buffer pointer to the start of the frame
	//default is high, set pin low to reset
	LATFCLR = RRST; //set pin low
	LATFSET = RRST; //set pin high
}
```
2. [Enable Output](https://github.com/athulyasimon/ov7670_with_PIC32/blob/5ca605fe3d894c1da259ed6ebd53389eb1c3dc2d/main.c#L411-L414) - Pin F1 (defined as OE) is set low to enable the output of valid data on the D7-D0 pins.
```
void FIFO_output_enable(){
	//Set FIFO output enable to active (low) so that image can be read
	LATFCLR = OE;	
}
```
3. [Provide Clock Signal]() - A PWM signal is generated to provide a clock signal for the camera. A new byte is presented onto the D7-D0 pins (which can all be read at once by the PIC32) for every pulse of the clock. 
```
void rckInitialize(){
	T3CONbits.TCKPS = 0; // Timer 3 pre­scaler N = 1 (1:1), thus it ticks at 80 Mhz (PBCLK/N)
	PR3 = FULL_DUTY - 1; // This makes run at 80 Mhz / (N * (PR3+1)) == 10 MHz
	TMR3 = 0; // Set the initial timer count to 0
	OC1CONbits.OCM = 0b110; // PWM mode without the failsafe for OC1
	OC1CONbits.OCTSEL = 1; // use timer 3
	OC1RS = FULL_DUTY/2; // Next duty duty cycle is 0
	OC1R = FULL_DUTY/2; // Initial duty cycle of 0
	T3CONbits.ON = 1; // Turn on timer 3
	OC1CONbits.ON = 1; // Turn on output compare 1
}
```
####Camera Output Manipulation
####Frame Resolution

The OV7670 is capable of outputting images with various frame resolutions. 


####Color Format


####Test Patterns


##Matlab
####uart/Reading in image

####yuv to rgb conversion


##Helpful Links
[My Project Code](https://github.com/athulyasimon/ov7670_with_PIC32).
[OV7670 Datasheet](http://www.voti.nl/docs/OV7670.pdf)
[PIC32MX795F512L Datasheet](http://ww1.microchip.com/downloads/en/DeviceDoc/61156G.pdf)



