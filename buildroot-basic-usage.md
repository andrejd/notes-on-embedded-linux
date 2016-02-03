## Working with Buildroot

#### Getting Buildroot

First you need to download Buildroot from their page with git; run this command inside your project directory:

```
git clone git://git.buildroot.net/buildroot
```

> In order to use Buildroot, some software has to be already installed on the
> host system (see list in [Buildroot documentation](http://buildroot.uclibc.org/downloads/manual/manual.html#getting-buildroot)). On Ubuntu Linux you only need to install package `libncurses5-dev` and `git` using `apt-get`.

Buildroot will be cloned into `buildroot` directory, and you can `cd buildroot/` to start working.

#### Loading and saving configuration

>To use buildroot provided example configurations, you would first run `$ make xyz_defconfig` which will copy settings for selected board to buildroot's `.config` file under buildroot's root directory and afterward you would run `make menuconfig` to edit new configuration.

Buildroot is a set of make files. First you have to create basic configuration for your new embedded Linux system
```bash
# This will open configuration menu where you can start modifying your system from scratch.
# After you are done with changes, save and close settings menu. This will create .config
# file in buildroot directory and this configuration will be used when running make command.
make menuconfig

# You can save configuration values for later use to selected directory or
# in .defconfig file in Buildroot directory if no path is provided
make savedefconfig BR2_DEFCONFIG=<path to already existing defconfig file>

# Saved configuration can be loaded later:
make defconfig BR2_DEFCONFIG=<path to already existing defconfig file>

# After loading custom configuration you need to run `make menuconfig`
make menuconfig

# And finally, you can create Linux images by executing make command.This will effectively
# take settings in `.config` file and start build process of downloading source files,
# compiling them and at the end generating images which can be uploaded to NAND flash
# on Linux board.
make
```
### Typical daily work flow

Usually you open `.config` file with `make menuconfig` and update configuration. Then you exit configuration menu (settings are saved to `.config` file) and save newly created configuration with `make savedefconfig` which stores it to defined directory. Then you run `make` which effectively build desired images


### Buildroot directories

#### `buildroot/dl` directory

In this directory all downloaded stuff is stored. From packages which will be installed in `rootfs`, linux source, uboot and cross-compiler ...

#### `buildroot/boards/<manufacturer>/<boardname>` directory

Directory contains stuff for specific board; here you can add all board related files: *fs_overlay* tree, scripts that should run after or before build, ....

#### `buildroot/configs` directory

This directory is meant to store default configuration files for different boards. Those `*_defconfig` files can be good place to start inspecting configurations of boards similar to your custom board ...
