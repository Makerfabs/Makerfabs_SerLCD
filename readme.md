# Makerfabs SerLCD（Not Ready）


```c++
/*
Version:		V1.0
Author:			Vincent
Create Date:	2021/3/13
Note:
	
*/
```


![](md_pic/main.jpg)


[toc]

# Makerfabs

[Makerfabs home page](https://www.makerfabs.com/)

[Makerfabs Wiki](https://makerfabs.com/wiki/index.php?title=Main_Page)

# Makerfabs SerLCD


## Intruduce

Product Link ：[Makerfabs SerLCD]() 

Wiki Link :  [Makerfabs SerLCD Wiki]() 

The Makerfabs SerLCD is an AVR-based, serial enabled LCD that provides a simple and cost effective solution for adding a 16x2 Black on RGB Liquid Crystal Display into your project. This display can now communicate in three different ways: serial, I2C, and SPI. 

The on-board ATmega328P AVR microcontroller utilizes 11.0592 MHz crystal for greater communication accuracy with adjustable baud rates of 1200 through 1000000 but is default set at 9600. The firmware for this SerLCD is fully opensource and allows for any customizations you may need.



## Feature

- Working voltage: 3.3V
- Interface: IIC, SPI, UART
- 16x2, Black on RGB Display
- The AVR ATMega328p (with Arduino-compatible bootloader) is populated on the back of each LCD screen and handles all of the LCD control
- Adjustable I2C address controlled via software special commands (0x72 default)
- Emergency reset to factory settings (Jumper RX to GND on bootup)
- Operational backspace character
- Incoming buffer stores up to 80 characters
- Pulse width modulation of backlight allows direct control of backlight brightness and current consumption
- Pulse width modulation of contrast allows for software defined contrast amount.
- User definable splash screen
- Open-sourced firmware and Arduino-compatible bootloader enables updates via the Arduino IDE
- I/O logic levels: 3.3V

### Front:

![front](md_pic/front~1.jpg)

### Back:
![back](md_pic/back~1.jpg)




## Equipment list

- SerLCD


## Compiler Options
**If you have any questions，such as how to install the development board, how to download the code, how to install the library. Please refer to :[Makerfabs_FAQ](https://github.com/Makerfabs/Makerfabs_FAQ)**

- Install library : [SoftPWM](https://github.com/bhagman/SoftPWM).
- Install library : [LiquidCrystalFast](https://www.pjrc.com/teensy/arduino_libraries/LiquidCrystalFast_1.1.zip).
- Upload codes, select "Arduino UNO"



## Code Explain

The fireware is changed from [sparkfun/OpenLCD](https://github.com/sparkfun/OpenLCD) and [teensy/td_libs_LiquidCrystal](https://www.pjrc.com/teensy/td_libs_LiquidCrystal.html)

- Control Command

```c++
/*
 OpenLCD is an LCD with Serial/I2C/SPI interfaces.
 By: Nathan Seidle
 SparkFun Electronics
 Date: April 19th, 2015
 License: This code is public domain but you buy me a beer if you use this and we meet someday (Beerware license).

 OpenLCD gives the user multiple interfaces (serial, I2C, and SPI) to control an LCD. SerLCD was the original
 serial LCD from SparkFun that ran on the PIC 16F88 with only a serial interface and limited feature set.
 This is an updated serial LCD.
 
 This example shows how to read a trimpot and use it to control the contrast settings on OpenLCD. Twisting
 the trimpot will send a new contrast setting to OpenLCD.
 
 Please Note: 0x72 is the default 7-bit I2C address. If you are using a different language than Arduino you will probably
 need to add the Read/Write bit to the end of the address. This means the default read address for the OpenLCD
 is 0b.1110.0101 or 0xE5 and the write address is 0b.1110.0100 or 0xE4.
 For more information see https://learn.sparkfun.com/tutorials/i2c

 Note: This code expects the display to be listening at the default I2C address. If your display is not at 0x72, you can
 do a hardware reset. Tie the RX pin to ground and power up OpenLCD. You should see the splash screen 
 then "System reset Power cycle me" and the backlight will begin to blink. Now power down OpenLCD and remove 
 the RX/GND jumper. OpenLCD is now reset.

 To get this code to work, attached an OpenLCD to an Arduino Uno using the following pins:
 SCL (OpenLCD) to A5 (Arduino)
 SDA to A4
 VIN to 5V
 GND to GND
 
 Hook a trimpot up:
 Pin 1 - 5V
 Pin 2 - A0
 Pin 3 - GND
 
 The OpenLCD has 4.7k pull up resistors on SDA and SCL. If you have other devices on the
 I2C bus then you may want to disable the pull up resistors by clearing the PU (pull up) jumper.
 
 OpenLCD will work at 400kHz Fast I2C. Use the .setClock() call shown below to set the data rate 
 faster if needed.

Command cheat sheet:
 ASCII / DEC / HEX
 '|'    / 124 / 0x7C - Put into setting mode
 Ctrl+c / 3 / 0x03 - Change width to 20
 Ctrl+d / 4 / 0x04 - Change width to 16
 Ctrl+e / 5 / 0x05 - Change lines to 4
 Ctrl+f / 6 / 0x06 - Change lines to 2
 Ctrl+g / 7 / 0x07 - Change lines to 1
 Ctrl+h / 8 / 0x08 - Software reset of the system
 Ctrl+i / 9 / 0x09 - Enable/disable splash screen
 Ctrl+j / 10 / 0x0A - Save currently displayed text as splash
 Ctrl+k / 11 / 0x0B - Change baud to 2400bps
 Ctrl+l / 12 / 0x0C - Change baud to 4800bps
 Ctrl+m / 13 / 0x0D - Change baud to 9600bps
 Ctrl+n / 14 / 0x0E - Change baud to 14400bps
 Ctrl+o / 15 / 0x0F - Change baud to 19200bps
 Ctrl+p / 16 / 0x10 - Change baud to 38400bps
 Ctrl+q / 17 / 0x11 - Change baud to 57600bps
 Ctrl+r / 18 / 0x12 - Change baud to 115200bps
 Ctrl+s / 19 / 0x13 - Change baud to 230400bps
 Ctrl+t / 20 / 0x14 - Change baud to 460800bps
 Ctrl+u / 21 / 0x15 - Change baud to 921600bps
 Ctrl+v / 22 / 0x16 - Change baud to 1000000bps
 Ctrl+w / 23 / 0x17 - Change baud to 1200bps
 Ctrl+x / 24 / 0x18 - Change the contrast. Follow Ctrl+x with number 0 to 255. 120 is default.
 Ctrl+y / 25 / 0x19 - Change the TWI address. Follow Ctrl+x with number 0 to 255. 114 (0x72) is default.
 Ctrl+z / 26 / 0x1A - Enable/disable ignore RX pin on startup (ignore emergency reset)
 '-'    / 45 / 0x2D - Clear display. Move cursor to home position.
        / 128-157 / 0x80-0x9D - Set the primary backlight brightness. 128 = Off, 157 = 100%.
        / 158-187 / 0x9E-0xBB - Set the green backlight brightness. 158 = Off, 187 = 100%.
        / 188-217 / 0xBC-0xD9 - Set the blue backlight brightness. 188 = Off, 217 = 100%.
         For example, to change the baud rate to 115200 send 124 followed by 18.
 '+'    / 43 / 0x2B - Set Backlight to RGB value, follow + by 3 numbers 0 to 255, for the r, g and b values.
         For example, to change the backlight to yellow send + followed by 255, 255 and 0.

*/
```
