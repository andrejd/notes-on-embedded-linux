## Tools

#### Atmel SAM-BA In-system Programmer
Atmel SAM-BA® software provides an open set of tools for programming the Atmel SAMA5, SAM3, SAM4, SAM7 and SAM9 ARM® core-based microcontrollers.

It is useful to program the NAND flash mounted on Acqua SoM. SAM-BA is available for Windows and Linux. This article illustrates how to install and use it on Linux.

Go to the Sam-ba download page on [Atmel SAM-BA In-system Programmer](http://www.atmel.com/tools/ATMELSAM-BAIN-SYSTEMPROGRAMMER.aspx)
and download file:

* SAM-BA 2.15 for Linux (sam-ba_2.15.zip, 02/2015)

Move the files to desired directory and extract it:

```
unzip sam-ba_2.15.zip
```

Move inside the `sam-ba_cdc_linux` just created `cd sam-ba_cdc_linux` and launch SAM-BA application:

```
sudo ./sam-ba_64
```

>Board has to be in RomBOOT mode for usb device to be visible by Linux, make sure that correct jumper is enabled,
>on Berta board pin BOOT has to be connected to GND.

SAM-BA can be also used as a command line tool in combination with .tcl scripts for automated flash programming.


#### Load `usbserial` module

Try to remove the usbserial module:

```
rmmod usbserial
```

If you get error: `Module usbserial is in use by: ftdi_sio` first remove the `ftdi_sio` module and then `usbserial`:

```
sudo rmmod ftdi_sio
sudo rmmod usbserial
```
Re-load `usbserial` kernel module

```
sudo modprobe usbserial vendor=0x03eb product=0x6124
```
Insert the Atmel board and check which device was created with `dmesg` and select newly created device in SAM-BA tool connection dropdown. Usually it is called `/dev/ttyACM0`

#### Cross compiler tool-chain

Install the GCC, G++ cross compilers and support programs:

```
$ sudo apt-get install libc6-armel-cross libc6-dev-armel-cross
$ sudo apt-get install binutils-arm-linux-gnueabi
$ sudo apt-get install libncurses5-dev
```

If you are using an Arietta, Aria or FOX board:
```
$ sudo apt-get install gcc-arm-linux-gnueabi
$ sudo apt-get install g++-arm-linux-gnueabi
```

If you are using an Acqua board:

```
$ sudo apt-get install gcc-arm-linux-gnueabihf
$ sudo apt-get install g++-arm-linux-gnueabihf
```

These commands for Acqua install `hard float` version of compiler. You can also use same compiler as for Arietta or Aria board with Acqua, which uses software float arithmetics.

Check version of cross-compiler with `arm-linux-gnueabihf-gcc -v`. You should see `gcc version 4.8.2 (Ubuntu/Linaro 4.8.2-16ubuntu4)`

Now you are ready to cross-compile on your PC all the source available for the Acme Boards based on Atmel MPUs.

> Buildroot does not support the distribution toolchain (i.e the gcc/binutils/C library installed by your distribution) as the toolchain to build software for the target. This is because distribution toolchain is not a "pure" toolchain (i.e only with the C/C++ library), so we cannot import it properly into the Buildroot build environment. So even if you are building a system for a x86 or x86_64 target, you have to generate a cross-compilation toolchain with Buildroot or crosstool-NG.
