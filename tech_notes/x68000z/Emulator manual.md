
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

X68000 emulator supports the `XDF` floppy disk image file format. There are plans to support hard drives in the future.

## 1.1 Starting the emulator

## 1.2 Turning off the emulator

## 1.3 Loading the floppy disc images

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

If no image with the filename `automount.xdf` is present, you can load multiple images onto your SD card. Upon inserting it into the X68000Z, you will be presented with a selection screen, where you can pick which image to boot. 
Use cursor keys to navigate the menu, Enter or Spacebar to confirm.
Select [cancel] (or press ESC) to exit the screen or simply eject the SD card.


(TODO: something about mouse and joystick?)


## 1.4 Emulator's technical specification
