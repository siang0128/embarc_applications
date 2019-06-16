# Voice Control Car
This application is designed to control car by voice. Extending to control anything we want by voice.

<!-- markdown-toc start - Don't edit this section. Run M-x markdown-toc-refresh-toc -->
- [Introduction](#introduction)
    - [Function](#function)
    - [System Architecture](#system-architecture)
- [Hardware and Software Setup](#hardware-and-software-setup)
    - [Required Hardware](#required-hardware)
    - [Required Software](#required-software)
    - [Hardware Connection](#hardware-connection)
- [User Manual](#user-manual)
    - [Before Running This Application](#before-running-this-application)
    - [Run This Application](#run-this-application)
- [Demo Video](#demo-video)
 <!-- markdown-toc end -->

# Introduction
We can control the car by voice and pass the control message to the remote car.
## Function
## System Architecture
   - The **LD-3320** listens to user and recognize the voice to commend, and the pass to ARC.
   - The **ARC** receives the message and parse the message to PWM value for car.
   - The **esp8266** transmits the message to car by WiFi (Remote control).
   - The **Rpi3** gets the PWM value and pass to STM32 that don't have WiFi module.
   - The **STM32* outputs the PWM to the pair of wheel.
   ![image](https://github.com/siang0128/Voice_Control_Car/blob/master/picture/Architecture.JPG)
# Hardware and Software Setup
## Required Hardware   
   - 1 DesignWare ARC EM Starter Kit(EMSK)
   - 1 Posture sensor(JY-901)
   - 1 Voice composite module(SYN6288)
   - 1 AD converter(PmodAD2)
   - 1 OLED display module(SSD1306)
   - 1 Voice recognition module(YS-LDV7)
   - 1 GPS module(C3-470A)
   - 1 GPRS module(SIM900A)
   - 1 SD Card
   
## Required Software
   - ARC GNU Toolset
   - Serial port terminal, such as putty, tera-term or minicom

## Hardware Connection
   1. The EMSK implement smart gloves,it will processe the data collected by sensors and translate gestures to corresponding sign language, we can get the corresponding sign language information by displaying text and making voice.
      - Connect **Posture sensor** to **J1**(Using UART interface)
      - Connect **Voice composite module**, **AD converter** and **GPRS module** to **J4**(Using I2C interface)
      - Connect **GPS module** to **J5**(Using UART interface)
      - Connect **OLED display module** to **J2**(Using I2C interface)
      - Connect **Voice recognition module** to **J3**
   2. Configure your EMSKs with proper core configuration
   - EMSK connection
   ![image](https://github.com/mk997630105/Master-Sign-Language/blob/master/Screenshots/connection.jpg)
   
# User Manual
## Before Running This Application
  Download source code of Master Sign Language from github.
  - The hardware resources are allocated as following table.
  
| Hardware Resource  | Functioon                      |
| ------------------ | -----------------------------  |
| JY-901             | Acceleration and angle sensor  |
| Flex2.2            | Flex sensor                    |
| SYN6288            | Voice composite                |
| C3-470A            | position                       |
| SIM900A            | send messages                  |
  
## Run This Application
  Here take EMSK2.0 - ARC EM7D with GNU Toolset for example to show how to run this application.We need to use embARC bootloader to automatically load application binary for different EMSK and run. See embARC Secondary Bootloader Example for reference.
  * Modify emsk_init.c (/board/emsk/common/emsk_init.c)
 ```
line 107: change 
	set_pmod_mux(mux_regs, PM1_UR_UART_0 | PM1_LR_SPI_S	\
				| PM2_I2C_HRI			\
				| PM3_GPIO_AC			\
				| PM4_I2C_GPIO_D		\
				| PM5_UR_SPI_M1 | PM5_LR_GPIO_A	\
				| PM6_UR_SPI_M0 | PM6_LR_GPIO_A );
 to 
 	set_pmod_mux(mux_regs, PM1_UR_UART_0 | PM1_LR_GPIO_A	\
				| PM2_I2C_HRI			\
				| PM3_GPIO_AC			\
				| PM4_I2C_GPIO_D		\
				| PM5_UR_SPI_M1 |PM5_LR_SPI_M2	\
				| PM6_UR_SPI_M0 | PM6_LR_GPIO_A );
```
### Makefile
   - Target options about EMSK and toolchain

```
         BOARD ?= emsk
         BD_VER ?= 20
         CUR_CORE ?= arcem7d
         TOOLCHAIN ?= gnu
```
   - The relative series of the root directory, here the path of the Makefile is .\embARC\example\emsk\sign language/makefile:
```
         #
         # root dir of embARC
         #
         EMBARC_ROOT = ../../..
         MID_SEL = common u8glib
```  
  See [ embARC Example User Guide][40], "Options to Hard-Code in the Application Makefile" for more detailed information about Makefile Options
  
  - Driver<br/>
  Placing the drivers' source code in driver folder, you can see there are subfolders for data,judge,draw and gprs drivers. Placing the C source file and header file in the corresponding subfolder.

| folder/file      | Function                                           |
| ---------------- | -------------------------------------------------  |
| data             | get and deal with data from sensor                 |
| draw             | OLED driver                                        |
| gprs             | send position information                          |
| judge            | match the collected data with the sample database  |

# Demo Video
[Link](http://v.youku.com/v_show/id_XMzYyNzM2NTc1Mg==.html?spm=a2h3j.8428770.3416059.1)


[40]: http://embarc.org/embarc_osp/doc/embARC_Document/html/page_example.html   " embARC Example User Guide"
