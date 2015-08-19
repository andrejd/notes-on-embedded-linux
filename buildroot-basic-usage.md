## Working with Buildroot

First you need to download Buildroot from their page with git; run this command inside your project directory:

> In order to use Buildroot, some software has to be already installed on the
> host system (see list in [Buildroot documentation](http://buildroot.uclibc.org/downloads/manual/manual.html#getting-buildroot)). On Ubuntu Linux you only need to install package `libncurses5-dev` and `git` using `apt-get`.

    $ git clone git://git.buildroot.net/buildroot

Buildroot will be cloned into buildroot directory, and you can `cd buildroot/` to start working.

---
### Basic commands

Buildroot is a set of make files. First you have to create basic configuration for your new embedded Linux system with 
    
    $ make menuconfig

This will open configuration menu where you can modify settings to meet your needs. After you are done with changes, save and close settings menu. This will create `.config` file in buildroot directory and this configuration will be used when running `make` command. 

You can save configuration values for later use with 
    
     $ make savedefconfig [BR2_DEFCONFIG=~/Development/Buildroot/mini6935/mini6935_defconfig]
which will save configuration file to the directory set in configuration, or in optionally passed path as an argument or in `.defconfig` file in Buildroot directory if none of the above options are defined.    

You can load saved configuration later with

    $ make defconfig [BR2_DEFCONFIG=~/Development/Buildroot/mini6935/mini6935_defconfig]
which will write configuration to `.config` file. After that you need to run `make menuconfig` to load them.

And finally, you can create Linux images by executing

    $ make

This will effectively take settings in `.config` file and start build process of downloading source files, compiling them and at the end generating images which can be uploaded to NAND flash on Linux board.

>To use buildroot provided example configurations, you would first run `$ make xyz_defconfig` which will copy settings for >selected board to buildroot's `.config` file under buildroot root directory and afterward you would run `make menuconfig` to >edit new configuration.

---

### Typical daily work flow

Usually you open `.config` file with `make menuconfig` and update configuration. Then you exit configuration menu (settings are saved to `.config` file) and save newly created configuration with `make savedefconfig` which stores it to defined directory. Then you run `make` which effectively build desired images

---  


### Buildroot directories

#### `buildroot/dl` directory

In this directory all downloaded stuff is stored. From packages which will be installed in `rootfs`, linux source, uboot and cross-compiler ...

#### `buildroot/boards/<manufacturer>/<boardname>` directory

Directory contains stuff for specific board; here you can add all board related files: *fs_overlay* tree, scripts that should run after or before build, ....

#### `buildroot/configs` directory

This directory is meant to store default configuration files for different boards. Those `*_defconfig` files can be good place to start inspecting configurations of boards similar to your custom board ...

---

### Cross-compiler

Once you have build your target for the first time, cross-compiler tool-chain was created for you. You can move that tool-chain to some other place to avoid recreating it each time you build your image from scratch. You can move created files to some arbitrary location and point to them in buildroot configuration file. 




 















