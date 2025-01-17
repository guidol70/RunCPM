# RunCPM - Z80 CP/M 2.2 emulator

**** Latest version updated. This Github repo was originally created against version 4.1 of RunCPM.
A new version has been created based on version 5.1 (work kindly done by Guido Lehwalder)
Thanks to him for the work in patching this version. I have put a copy of his work in the folder called
RunCPM_VGA32_v5_1. It is not a full copy of RunCPM, just the version that can be compiled for
the ESP-32 as described below.

**** This version has been modified (in progress) to use the the FABGL library for ESP32 ****

Please see http://www.fabglib.org/ for the library used. Note the version used is installed from the Arduino IDE (the version on the github is improved but not compatable with the Arduino version, if anyone wants to help or have a go at using the github version go for it, I may try myself time permitting.)

You get a full 80x34 console on a VGA screen and keyboard. It can save your preferences (in the same way as ANSI TERMINAL Does.)

No changes have been made the the other versions of RunCPM, just the esp version.

This has been done as a fork for easy updates between several computers i work on. Ultimatly i'm hoping i can convince the auther to merge these changes into the master branch. If you use this idea and version please let me know how you get on.

# IMPORTANT NOTES:

I have used this version of RunCPM on two ESP32 systems so far

1, The VGA32 ESP V1.4, a popular self conatined board with VGA/Keyboard/Mouse/SDcard all in one. See bellow for issues/fixes

2, Homemade version based on the wroom module, with SDCARD/Keyboard and VGA output added as per FABGL generic circuit. See wiki for a picture of it.

so far:

My version of the hardware that used the SD card GPIO's that where suggested on the generic circuit and works perfectly.

However, the settings for the VGA32 ESP seem to have a conflict on the GPIO settings for the SD card. It does work but to upload you have to remove the SD card and after a soft reset you have to remove the SD card and then replace it followed by another reset. It works ok from a power on or hard reset. If you can help fix this please let me know.
UPDATE 11Oct2020:
The VGA32 ESP v1.4 uses GPIO12 (MTDI, read on pwerup/reset). This pin determines the voltage for the RAM, Voltage of Internal LDO (VDD_SDIO). When the SD card is present it pulls this pin high and the ESP32 thinks that the RAM voltage should be 1.8V, without the SD card this pin has internal pull-down to set the RAM voltage to 3.3V.
This is actually mentioned in the notes from examples provided on the FABGL project, to quote:
<pre>
 notes about GPIO 2 and 12
    - GPIO2:  may cause problem on programming. GPIO2 must also be either left unconnected/floating,
              or driven Low, in order to enter the serial bootloader.
              In normal boot mode (GPIO0 high), GPIO2 is ignored.
    - GPIO12: should be avoided. It selects flash voltage. To use it disable GPIO12 detection setting efuses with:
                    python espefuse.py --port /dev/cu.SLAB_USBtoUART set_flash_voltage 3.3V
                       WARN!! Good for ESP32 with 3.3V voltage (ESP-WROOM-32). This will BRICK your
                              ESP32 if the flash isn't 3.3V
                       NOTE1: replace "/dev/cu.SLAB_USBtoUART" with your serial port
                       NOTE2: espefuse.py is downloadable from https://github.com/espressif/esptool
</pre>
This further notes that GPIO12 should be avoided, a pitty no one told the maker of the VGA32 ESP card!

FIXED: I have tried the option of setting the flash voltage to overide the use of GPIO12 and can confirm on my vga32 ESP, it Works! Problem fixed no more issues with the SD Card. I urge you to check, double check and think carefully about creating a BRICK if you decide to try it as well. I am not recommending it as an action, but for me it worked.

UPDATE 13oct2020:
When you build this version, make sure you use the IDE library manager to add the FabGL libraries, If you use the version on Github you may need to make changes to the Menu Dialogue. There is a difference between the two.

![https://github.com/coopzone-dc/RunCPM/blob/master/Docs/IMG_0561.jpg](https://github.com/coopzone-dc/RunCPM/blob/master/Docs/IMG_0561.jpg)

![https://github.com/coopzone-dc/RunCPM/blob/master/Docs/IMG_0563.jpg](https://github.com/coopzone-dc/RunCPM/blob/master/Docs/IMG_0563.jpg)

***************************************************************************************

# Original text continues....

RunCPM is an application which can execute vintage CP/M 8 bits programs on many modern platforms, like Windows, Mac OS X, Linux, FreeBSD, MS-DOS, Arduino DUE and variants, like the Teensy or ESP32. It can be built both on 32 and 64 bits host environments and should be easily portable to other platforms.<br>
RunCPM is fully written in C and in a modular way, so porting to other platforms should be only a matter of writing an abstraction layer file for it. No modification to the main code modules should be necessary.

If you miss using powerful programs like Wordstar, dBaseII, mBasic and others, then RunCPM is for you. It is very stable and fun to use.<br>
RunCPM emulates CP/M 2.2 from Digital Research as close as possible, the only difference being that it uses regular folders on the host instead of disk images.

RunCPM was written to serve as a test environment when I was restoring the only copy of Z80 MicroMumps v4.06 which exists online (https://github.com/MockbaTheBorg/MicroMumps).<br>
Making changes, recompiling MicroMumps and loading it onto a regular CP/M emulator via a disk image every time I moved a bit forward on the restoration was becoming too time consuming. So I decided to write something that would allow me to run the executable right away after making any modifications.<br>
RunCPM then evolved as more and more CP/M applications were added to its compatibility list.

RunCPM builds on Visual Studio 2013 or later. Posix builds use GCC/LLVM. It can also be built on the Arduino IDE and even on DJGPP for DOS. It can be built also on Cygwin (posix) and Mingw. Makefiles are provided with the distribution.

## Arduino / Teensy / ESP32 / STM32

RunCPM builds on Arduino IDE 1.8.7 or later.<br>
RunCPM so far runs on the Arduino DUE, on the Teensy 3.5, 3.6 and 4.0, on the ESP32 and on some STM32, as it requires a fair amount of RAM to run (64K used to be a lot back in those days).<br>
If using the Arduino DUE, RunCPM also needs a SD (or microSD) card shield to place the CP/M files in. The Teensy has an on-board microSD adapter. Some ESP32 and STM32 boards may need external SD card adapters.

Arduino digital and analog read/write support was added by Krzysztof Kliś via extra non-standard BDOS calls (see the bottom of cpm.h file for details).

LED blink codes: Arduino/Teensy/ESP32/STM32 user LED will blink fast when RunCPM is waiting for a serial connection and will send two repeating short blinks when RunCPM has exited (CPU halted). Other than that the user LED will indicate disk activity.

RunCPM needs A LOT of RAM and Flash memory by Arduino standards, so it will NOT run on other Arduinos than the DUE (not the Duemilanove) as they will not have enough of those.
It is theoretically possible to run it on an Arduino which has enough Flash (at least 96K) by adding external RAM to it via some shield, but this is untested, probably slow and would require an entirely different port of RunCPM code.

When using Arduino boards, the serial speed as well as other parameters, may be set by editing the RunCPM.ino sketch. The default serial speed is 9600 for compatibility with vintage terminals.<br>
There are also board definition files under the "hardware" folder which need to be reviewed before building.

Usage of SdFatSoftSpiEX and SdFatEX will require changes to the following SdFat lines on SdFatConfig.h:<br>
    #define ENABLE_EXTENDED_TRANSFER_CLASS (from 0 to 1 - around line 71)<br>
    #define ENABLE_SOFTWARE_SPI_CLASS (from 0 to 1 - around line 87)

If building for the Teensy, ESP32 and STM32, please read the entire document, as there is more information below.

## Experimental Platforms

Atmel SAM4S Xplained Pro.<br>
Altera/Intel DE1 Cyclone II FPGA (NiosII).

## Building

RunCPM builds natively on Visual Studio.

For building on other systems run `make yyy build`, where "yyy" is:

* **dos** - when building with DJGPP under MS-DOS (deprecated),
* **macosx** - Mac OS X,
* **mingw** - when building with MinGW under Windows,
* **posix** - when building under Linux, FreeBSD etc,
* **tdm** - when building with TDM-GCC under Windows.

For Linux and FreeBSD the "ncurses.h" header file (and library) is required. The name of the package providing it depends on the Linux distribution. Some are named "ncurses-dev", some "ncurses-devel", so Google is your friend for that.<br>
On Mac OS X, install it using "brew install ncurses".<br>
The "readline.h" header file is also required on Linux/FreeBSD. On some linuxes it is called "libreadline-dev".

## Getting Started

**Preparing the RunCPM folder :**<br>
Create a folder containing both the RunCPM executable and the CCP binaries for the system. CCP Binaries for 64K and 60K are provided.<br>
If using a SD card, RunCPM and its CCPs need to be on the SD card's root folder.<br>
The 64K version CCPs will provide the maximum amount of memory possible to CP/M applications, but its addressing ranges are unrealistic in terms of emulating a real CP/M 2.2 computer.<br>
The 60K version CCPs will provide a more realistic addressing space, by keeping the CCP entry point on the same loading address it would be on a physical CP/M computer.<br>
Other amounts of memory can be used, but this would require rebuilding the CCP binaries (sources available on disk A.ZIP).
The CCP binaries are named with their file name extensions matching the amount of memory they run on, so for example, DRI's CCP runnin on 60K memory would be named CCP-DR.60K. RunCPM looks for the file accordingly, depending on the amount of memory selected when it is built.<br>
**IMPORTANT NOTE** - Starting on version 3.4, regardless of the amount of memory allocated to the CP/M system, RunCPM will still allocate 64K of RAM on the host so the BIOS is always at the same starting position. This favors the porting of even more different CCP codes to RunCPM. This also requires that, starting on version 3.4, new copies of the master disk A.ZIP, ZCPR2 CCP and ZCPR3 CCP (all provided here) are used.

**Preparing the CP/M virtual drives :**<br>
**VERY IMPORTANT NOTE** - Starting on RunCPM version 3.7 user areas are now mandatory. The support of non-user-area disk folders has been dropped between versions 3.5 and 3.6, so if you are running on a version up to 3.5, please consider moving to 3.7 or higher but not before updating your disk folders structure to match the support of user areas (see below).<br><br>

RunCPM emulates the CP/M disks and user areas by means of subfolders under the RunCPM executable location, to prepare a folder or SD card for running RunCPM use the following procedures:<br>
* **when using user areas** - Create subfolders under where the RunCPM executable is located and name them "A", "B", "C" and so on, for each disk drive you intend to use, each one of these folders will be one disk drive, and under folder "A" create a subfolder named "0". This is the user area 0 of disk A:, extract the contents of A.ZIP package into this "0" subfolder. Switching to another user area inside CP/M will automatically create the respective user area subfolders, "1", "2", "3" ... as they are selected. Subfolders for the user areas 10 to 15 are created as letters "A" to "F".
* **when not using user areas** - Create subfolders under where the RunCPM executable is located and name them "A", "B", "C" and so on, for each disk drive you intend to use, each one of these folders will be one disk drive, and only user area 0 is available. Switching to other user areas will be ignored. Extract the contents of the A.ZIP package into the "A" subfolder itself.

The support for user areas is the default, and it is mandatory from version 3.7 on, as this is the standard CP/M 2.2 behavior.

All the letters for folders/subfolders and file names should be kept in uppercase, to avoid any issues of case-sensitive filesystems compatibility.
CP/M only supported 16 disk drives: A: to P:, so creating other letters above P won't work, same goes for user areas above 15 (F).

**Available CCPs :**<br>
RunCPM can run on its internal CCP (beta) or using binary CCPs from real CP/M computers. A few CCPs are provided:

* **CCP-DR** - Is the original CCP from Digital Research.<br>
* **CCP-CCPZ** - Is the Z80 CCP from RLC and others.<br>
* **CCP-ZCP2** - Is the original ZCPR2 CCP modification.<br>
* **CCP-ZCP3** - Is the original ZCPR3 CCP modification.<br>
* **CCP-Z80** - Is the Z80CCP CCP modification, also from RLC and others.<br>

These CCPs are provided with their source code on the A.ZIP package, and can be natively rebuilt if needed.<br>
SUBMIT (.SUB) files are provided to allow for rebuilding the CCPs and some of the RunCPM utilities.<br>
Other CCPs may be adapted to work, and if succeeding, please share it so we can add it to here.

The default setting for CCP is the Digital Research one, changing it requires rebuilding the code.

**Contents of the "master" disk (A.ZIP) :**<br>
The master disk A.ZIP contains a basic initial CP/M environment, with the source code for the CCPs and also the **EXIT** program, which ends RunCPM execution.<br>
There is also a **FORMAT** program which creates a drive folder as if it was formatting a disk. It does nothing to pre-existing drive folders, so it is very safe to use.<br>
Disks created by **FORMAT** cannot be removed from inside RunCPM itself, if needed, it must be done manually by accessing the RunCPM folder or SD Card on a host machine and removing the disk drive folder.<br>
The master disk also contains **Z80ASM**, which is a very powerful Z80 assembly that generates .COM files directly.<br>
Other CP/M applications which were not part of the official DRI's distribution are also provided to improve the RunCPM experience. These applications are listed on the 1STREAD.ME file.

## Printing

Printing to the PUN: and LST: devices is allowed and will generate files called "PUN.TXT" and "LST.TXT" under user area 0 of disk A:. These files can then be tranferred over to a host computer via XMODEM for real physical printing.
These files are created when the first printing occurs, and will be kept open throughout RunCPM usage. They can be erased inside CP/M to trigger the start of a new printing.
As of now RunCPM does not support printing to physical devices.

## Lua Scripting Support

The internal CCP can be built with support for Lua scripting.<br>
Lua scripts can be written on the CP/M environment using any text editor and then executed as if they were CP/M extrinsic commands.<br>

The order of execution on the internal CCP when an extrinsic command is typed with no explicit drive is:<br>
* The command with extension .COM is searched on the current drive.<br>
* If not found it is searched on drive A: user area 0.<br>
* If not found it is searched on the current drive user area 0.<br>
* If not found then use .LUA extension instead and repeat the above search.

Lua scripts have access to these functions:
* **BdosCall(C, DE)** - **C** is the number of the function to call and **DE** the parameter to pass.<br>
  The C and DE CPU registers are loaded accordingly and the BDOS function if called.<br>
  The function returns the contents of the HL register upon returning from the BDOS call.
* **RamRead(addr)** - **addr** is the memory address to read from, the function returns a byte.
* **RamWrite(addr, v)** - **addr** is the memory address to write **v** to. **v** must be a byte.
* **RamRead16(addr)** - **addr** is the memory address to read from, the function returns a 16 bit word.
* **RamWrite16(addr, v)** - **addr** is the memory address to write **v** to. **v** must be a 16 bit word.
* **ReadReg(reg)** - **reg** is the 16 bit CPU register to read from, the function returns a 16 bit word.
* **WriteReg(reg, v)** - **reg** is the CPU register to write to. **v** must be a 16 bit word.
  Extra care must be taken when willing to replace only part of the 16 bit register.<br>

The **ReadReg** and **WriteReg** functions refer to the CPU registers as index values.
The possible values for **reg** on those functions are:<br>
```
 0: PCX - External view of PC
 1: AF
 2: BC
 3: DE
 4: HL
 5: IX
 6: IY
 7: PC
 8: SP
 9: AF'
10: BC'
11: DE'
12: HL'
13: IFF - Interrupt Flip Flop
14: IR - Interrupt (upper) / Refresh (lower) register
```
The disk A.ZIP contains an example script called LUAINFO.LUA, with the same functionality of INFO.COM, which provides information about RunCPM.

Caveat: Lua scripts must have a comment (--) on their last line, to prevent issues with the CP/M ^Z end-of-file character when the scripts are created with CP/M text editors. The comment on the last line comments out the CP/M EOF (^Z) character and prevents Lua interpreter errors.

## Limitations / Misbehaviors

The objective of RunCPM is **not** to emulate a Z80 CP/M 2.2 computer perfectly, but to allow CP/M to be emulated as close as possible while keeping its files on the native (host) filesystem.<br>
This will obviously prevent the accurate physical emulation of disk drives, so applications like **MOVCPM** and **STAT** will not be useful.<br>
They are still provided on the master disk A.ZIP just to maintain compatibility with DRI's official CP/M 2.2 distribution.

Other CP/M flavors like CP/M 3 or CP/M Plus are not supported as of yet, as the emulated BDOS of RunCPM is specific for CP/M 2.2.

The IN/OUT instructions are used to implement the communication between the soft CPU BIOS and BDOS and the equivalent RunCPM functions, therefore these instructions are unusable for any other purpose.

The "video monitor" is assumed to be ANSI/VT100 emulation, as this is the standard for DOS/Windows/Linux distributions. So CP/M applications which are hardcoded for other terminals won't build their screens correctly.<br>
When using a serial terminal emulator, make sure it sends either CR or LF when you press enter, not both (CR+LF), or else it will break the DIR listing on DR's CCP. This is standard CP/M 2.2 behavior.

RunCPM does not support making files read-only or any other CP/M attributes. All the files will be visible and R/W all the time, so be careful. It supports making "disks" read-only though, but only from RunCPM's perspective. The R/O attributes of the disk's containing folder are not modified.

Lua scripting is not supported on platforms other than Windows, Linux and MacOS. There is no Lua support for Arduino based platforms yet.

Some applications, like hi-tech C for example, will try to access user areas higher than 15 to verify if they are running on a different CP/M flavor than 2.2. This causes the generation of user areas with letters higher than F. This is an expected behavior and won't be "fixed".

There is a hardware-design-error on some Clones and some Revisions of the original
Arduino Due which prevents the correct use of the RX0-Pin (serial TTL-Port Receive-Pin)
while not using the USB-Programming-Port for accessing RunCPM on the Arduino Due.
The solution is to use the serial port 1 = RX1 and TX1
For that you have to replace all occurrences of "Serial." with "Serial1."
https://github.com/MockbaTheBorg/RunCPM/issues/117 - @Guidol70

## CP/M Software

I have shared my entire CP/M software library [here!](https://drive.google.com/drive/folders/11WIu8rD_7pIDaET7dqTeA73CvX0jkxz2?usp=sharing)<br>
Please, if you have newer/cleaner (closer to the original distribution) versions of the software found here, please consider sending it over so I can update.<br>
Same goes for user guides and other useful documentation.<br>
My intent is to keep here clean copies of useful software which has been tested and proven to work fine with RunCPM.

## Online contact/support

https://discord.gg/WTTWVZ6 - I have created this discord channel to be able to (eventually) meet people in real time and talk about RunCPM. Feel free to join.

## References

https://weblambdazero.blogspot.com/2016/07/cpm-on-stick_16.html<br>
https://lehwalder.wordpress.com/2017/04/17/cpm-auf-dem-arduino-due-mit-runcpm-bzw-cpmdunino/ - in German<br>
https://hackaday.io/project/19560-z80-cpm-computer-using-an-arduino<br>
http://www.chstercius.cz/runcpm/<br>
https://hackaday.com/2014/12/30/z80-cpm-and-fat-file-formats/<br>
https://ubuntuforum-br.org/index.php?topic=120787.0 - in Portuguese<br>
http://mrwrightteacher.net/retrochallenge2018/<br>
https://learn.adafruit.com/z80-cpm-emulator-for-the-samd51-grand-central<br>
https://forum.armbian.com/topic/8569-runcpm-on-armbian-cpm-weekend-fun/<br>
https://blog.hackster.io/z80-cp-m-emulator-runs-on-adafruits-new-grand-central-dev-board-6a28ad73dfbc<br>
https://ht-deko.com/arduino/runcpm.html - in Japanese<br>

## RunCPM compatible boards tested so far

https://store.arduino.cc/usa/arduino-due<br>
https://www.pjrc.com/store/teensy35.html<br>
https://www.pjrc.com/store/teensy36.html<br>
https://github.com/LilyGO/ESP32-TTGO-T1 - LED on pin 22<br>
https://wiki.wemos.cc/products:lolin32:lolin32_pro - LED on pin 5 (inverted)<br>
https://docs.zerynth.com/latest/official/board.zerynth.doit_esp32/docs/index.html - LED on pin 2<br>
https://docs.espressif.com/projects/esp-idf/en/latest/get-started/get-started-pico-kit.html - No user LED on board<br>
https://www.st.com/en/evaluation-tools/stm32f4discovery.html - SD on Software SPI mode (mounted on EB-STM32F4DISCOVERY-LCD)<br>
https://www.adafruit.com/product/4064<br>
https://github.com/SmartArduino/SZDOITWiKi/wiki/ESP8266---ESPduino-32<br>

I have seen reports of people being able to run it on the Nucleo F401re, Nucleo F411 and a japanese board called GR-SAKURA.
It was also successfully built and ran on the Kindle Keyboard 3G.

## Building dependencies

For the Arduino DUE, support for it needs to be added through the board manager.<br>
For the Teensy follow the instructions from here: https://www.pjrc.com/teensy/td_download.html<br>
For the ESP32 follow the instructions from here: https://randomnerdtutorials.com/installing-the-esp32-board-in-arduino-ide-windows-instructions/<br>
For the STM32 follow the instructions from here: https://github.com/stm32duino/Arduino_Core_STM32<br>
All boards now use the SdFat library, from here: https://github.com/greiman/SdFat/<br>
All Arduino libraries can be found here: https://www.arduinolibraries.info/

## Teensy 4.1
Building for the Teensy 4.1 requires using Greiman's SdFat-beta library, as it is not supported (yet) by the regular SdFat.<br>
In order to build for the Teensy 4.1 download the SdFat-beta library and extract it inside the Arduino's Library Folder, on a subfolder named SdFat-beta, then make the following changes to it:<br>
* rename src/SdFat.h to src/SdFat-beta.h<br>
* in the library.properties file, change name=SdFat to name=SdFat-beta<br>

## ESP32 Limitations

The ESP32 build doesn't yet support the analogWrite BDOS call.<br>
The ESP32 build may require additional changes to the code to support different ESP32 boards.<br>
The ESP32 build uses the slower SPI mode for accessing the SD card.<br>

## STM32 Limitations

The STM32 build uses the slower SPI mode for accessing the SD card.<br>

## Dedications

I dedicate this software to:<br>
* *Krzysztof Kliś* -  For writing the Arduino hardware interface and helping with debugging/testing.<br>
* *Yeti* - For being an awesome agitator since RunCPM went on GitHub.<br>
* *Wayne Hortensius* - For going deep into the code and bringing even more CP/M 2.2 compatibility to disk routines.<br>
* All the other folks who are actively helping with finding/fixing bugs and issues on Github - thanks guys.<br>

I dedicate it also to the memory of some awesome people who unfortunately are not among us anymore:<br>
* *Mr. Gary Kildall* - For devising an operating system which definitely played a role on changing the world.<br>
* *Mr. Jon Saxton* - For finding the very first RunCPM bug back in 2014.<br>
* *Dr. Richard Walters* - For writing the Z80 version of mumps, one of the most useful and fun languages I had the joy to use.<br>
* *Mr. Tom L. Burnett* - For helping me with testing/debugging many different CP/M 2.2 applications on RunCPM.<br>
May the computers in heaven be all 8-bit.<br>

## Donations

Last but not least, if you feel like make a donation to help the project moving forward, feel free to do so by clicking [here!](http://paypal.me/MockbaTheBorg)<br>
These will help me source more hardware and other things that assist on the RunCPM development.
<hr>
###### The original copy of this readme file was written on WordStar 3.3 under RunCPM

