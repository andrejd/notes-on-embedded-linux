### AT91bootstrap for AcmeSystems Acqua A5 board

AT91bootstrap is the 2nd level bootloader for Atmel AT91 SoC providing a set of algorithms to manage the hardware initialization such as clock speed configuration, PIO settings, DDR2 DRAM initialization. After that it usually downloads 3rd level application, u-boot, to RAM and runs it from there.

The microprocessor after reset runs the RomBOOT code which is contained in the internal ROM memory. After basic initialization of the microprocessor, RomBOOT bootloader tries to load a valid program code from several peripheral devices in this order
* SPI0 CS0 Flash
* SD Card / eMMC MCI0
* SD Card / eMMC MCI1
* NAND Flash
* SPI0 CS1 Flash
* TWI EEPROM

When the RomBOOT successfully detects a valid program code, it loads the code from the device into the internal SRAM memory and executes it. If a valid code hasn't been found, bootloader starts the SAM-BA Monitor. Details of this topic can be found in the chapter *Standard boot strategies* of the SAMA5D3 microprocessor datasheet.

#### Download sources

Download from GitHub the Atmel AT91boostrap version 3.7 sources: [v3.7.zip](https://github.com/linux4sam/at91bootstrap/archive/v3.7.zip) and uncompress it by typing `unzip v3.7.zip` and move inside the new directory. Download the patch from GitHub repository: `wget https://raw.githubusercontent.com/AcmeSystems/acmepatches/master/at91bootstrap-3.7.patch` and apply the patch: `patch -p1 <  at91bootstrap-3.7.patch`. Patch will add needed defconfing files for AcmeSystems board, including for Acqua A5.

#### Configure at91bootstrap source tree

AT91bootstrap has to be configured for target board with correct *_defconfig* file. Configuration files are found in `board/<devboard-name>/` folder in source tree.

For Acqua A5 board with 256MB nand flash memory we need to use *acqua-256m_defconfig* configuration file with command `make acqua-256m_defconfig` to merge board settings with default values into _.config_ file in root folder which will be used for compilation.

> *acqua-256m_defconfig* by default configures AT91bootstrap for direct linux kernel loading to RAM. To first load u-boot, we need to modify current configuration with `make menuconfig` and then save new configuration to new *_defconfig file*

#### Modifications for Acqua A5 board

After we have valid *.config* file we need to make changes in current configuration for AT91bootstrap to load u-boot from NAND to RAM and to run it.
```bash
# change configuration
make menuconfig

# save configuration
make savedefconfig

# copy new configuration to board folder for later use
cp defconfig /board/sama5d3_acqua/acqua-256m-nandflash_defconfig
```
Here are the contents of used configuration for loading u-boot from NAND to RAM address 0x26f00000

```java
CONFIG_SAMA5D3_ACQUA=y
CONFIG_CPU_CLK_528MHZ=y
CONFIG_RAM_256MB=y
CONFIG_JUMP_ADDR="0x26f00000"
CONFIG_DEBUG=y
CONFIG_SCLK=y
```

#### Compiling AT91bootstrap

To launch the compilation type:

```bash
make CROSS_COMPILE=arm-linux-gnueabi-
```

> I got an error when compiling and I had to remove `void` from function call on line 572 in file sama5d3_acqua.c: `void nandflash_hw_init(void){ _nandflash_hw_init(void); }`

After compilation you can find created .bin file under `binaries/` folder. This file can be flashed with SAM-BA to board.
