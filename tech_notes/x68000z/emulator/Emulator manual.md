
1. Using the X68000 emulator
	1. Starting the emulator
	2. Turning off the emulator
	3. Loading the floppy disk images
		1. Loading SD cards included in the Early Access Kit
		2. Supported SD card types
		3. Second method of loading the floppy disk images
		4. Third method of loading the floppy disk images
	4. Emulator's technical specification

# Using the X68000 emulator

X68000 emulator supports the XDF floppy disk image file format. There are plans to support hard drives in the future.

## 1.1 Starting the emulator

The X68000 emulator can started in two ways - either by enabling it in the X68000Z setup utility or by clicking the emulator icon in the X68000Z game launcher.
For details about the setup utility and the game launcher, refer to their corresponding manuals.

## 1.2 Turning off the emulator

## 1.3 Loading the floppy disc images

Several methods of loading floppy disk images are supported.

*Note:
Provided SD cards with game floppy disk images cannot be run in the emulator. (Use X68000Z launcher for this.)*

### 1.3.1 Loading SD cards included in the Early Access Kit

In the Early Access Kit you will find a copy of the Human68k v1.0 operating system attached. Insert it into the SD card slot to start the system.
![[emulator_1_3_1.png]]

### 1.3.2 Supported SD card types

![[emulator_1_3_2.png]]
※ micro SDHC can also be used via the suitable adapter.

Prepare your SDHC card by mounting it into your PC computer and making sure it is formatted in the `FAT32` format. 
※  If the filesystem format is different, please format the card and change the format to `FAT32`

Prepare XDF files you wish to use and copy them onto your SDHC card.

### 1.3.3 Second method of loading the floppy disk images

If you name your file `automount.xdf` and place it at the top level of your SD card, the emulator will attempt to automatically mount and boot it.

![[emulator_1_3_2_2.png]]

### 1.3.4 Third method of loading the floppy disk images

![[Pasted image 20230610194604.png]]

If no image with the filename `automount.xdf` is present, you can load multiple images onto your SD card. Upon inserting it into the X68000Z, you will be presented with a selection screen, where you can pick which image to boot. 
Use cursor keys to navigate the menu, Enter or Spacebar to confirm.
Select [cancel] (or press ESC) to exit the screen or simply eject the SD card.

![[emulator_1_3_4_2.png]]

## 1.4 Emulator's technical specification

Underneath you will find the specifications of the X68000 emulator shipped with the X68000Z firmware version 1.0.0

#### Video output
- Frame buffer's size 768x512, HDMI 720P with DE scaling to 972x648 (with a 5% safety zone)
- X68000's maximum resolution is 768x512, Higher resolution's cannot be drawn (?)

#### Audio output
- HDMI output and headphone jack. Internal speaker output can also be enabled for the "X68000Z Launcher" (when headphones are not connected).

#### Floppy disk related
- Physically inserting and removing SD cards is being treated as inserting and removing floppy disks. You can press the [Eject] button to "virtually" remove the card from the system (the status LED indicator will turn off), Press [Eject] again to re-insert the card.
- Each SD card slot is treated as one floppy disk drive. If you want to insert two floppy images at once, you need to use two SD cards.
- You can use your own floppy disk images (in XDF format).
	- SDHC SD card format is supported (FAT32 filesystem, capacity up to 32GB)
	- Make sure to back up your floppy disk files.
	- Copy your XDF image files to the SD card to use them.
	
Files on the SD card are handled differently depending on the filenames and directory structure.
1. Single XDF file named `automount.xdf` in the card's top level directory:
	If such file exists on the SD card, it will be automatically mounted and booted upon inserting into the machine.
2. "X68000Z" directory in the card's top level directory:
	If no file named `automount.xdf` exists, a file to auto-mount will be picked from this directory. In case of multiple valid images, you will be able to choose them from a list.
	- Up to 100 files can be selected from. Even if the directory contains more than 100 valid image files, the list will show only up to 100.
	- To select the image, you need to use the keyboard.
	- Navigate the list with cursor keys, confirm your selection with [Enter] or [Spacebar].
	- To exit the selection screen, select [Cancel], press [ESC] or eject the SD card.

- Write-protected SD cards will be treated as write-protected floppy disks.
- Data written onto a "floppy drive" will be copied to the SD card when the eject operation is being performed. You can do it by:
	- via the X68000 software
	- pressing the physical [Eject] button
- After ejecting the SD card, the status LED will turn off, then you can remove the card.
- If you remove the SD card when the status LED is on, the data will not be written correctly.
- Keep in mind that resetting the machine does not perform the eject operation (so your data won't be written to the SD card).

#### RS-232C port
- Supported baud rates are:  **300bps, 600bps, 1200bps, 2400bps, 4800bps, 9600bps, 19200bps, 38400bps**. If you experience unstable connection, try lowering your baud rate.
- Hardware flow control is not supported.
- Setting stop bit to 1.5 on the X68000 side will be treated like 1 bit by the UART.

#### RTC
- Changing RTC settings in Human68k won't affect the built-in RTC (you should use X68000Z boot settings screen instead).
- The alarm feature is not supported.

#### Miscellaneous
- Only one keyboard and one mouse can be connected.
- Two DirectInput joypads can be connected. P1 and P2 will be assigned a joypad automatically.
- Disconnecting P1's joypad won't cause P2's joypad being re-assigned to P1.
	