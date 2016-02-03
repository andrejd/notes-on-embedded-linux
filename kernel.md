## Linux kernel

Linux kernel can be build directly by downloading source files or in the context of Buildroot scripts.

### Step-by-step Kernel cross-compilation procedure

#### 1. Kernel setup

Start with downloading kernel source tree and AcmeSystems patch for AcmeSystems boards

```bash
# download kernel sources
wget https://www.kernel.org/pub/linux/kernel/v4.x/linux-4.2.6.tar.xz

# untar downloaded archive
tar xvfJ linux-4.2.6.tar.xz

# go to newly created directory
cd linux-4.2.6

# At this point I suggest to create a your own personal branch of Linux Kernel
# sources with git to keep a trace of any changes from the Kernel mainline
git init
git add .
git commit -m "Linux vanilla"
git branch acme
git checkout acme

# download this patch file from GitHub:
wget https://raw.githubusercontent.com/AcmeSystems/acmepatches/master/linux-4.2.6.patch

# patch source tree with downloaded patch to add support for AcmeSystems SoM
patch -p1 < linux-4.2.6.patch

# Configure kernel by applying correct configuration file (e.g. for Acqua board)
# This will prepare source tree for our board and save configuration to .config file
make ARCH=arm CROSS_COMPILE=arm-linux-gnueabi- acme-acqua_defconfig

# kernel customization can now be inwoked by
make ARCH=arm menuconfig

```

#### 2. Generate the Device Tree Binary file (.dtb)

Compile the device tree file requested by your board by typing one of these commands:

```bash
# previously applied patch also provided source file for device tree (acme-acqua.dts)
# needed for device tree binary compilation, created with this step
make ARCH=arm CROSS_COMPILE=arm-linux-gnueabi- acme-acqua.dtb
```

#### 3. Compile the Kernel image

Compile the Linux Kernel sources and generate the binary compressed image file

```bash
# start compilation, image will be saved to arch/arm/boot/
make -j8 ARCH=arm CROSS_COMPILE=arm-linux-gnueabi- zImage
```

#### 4. Compile the Kernel modules

The image generated in step #3 contains the Linux Kernel and all the built-in device drivers (option [*] in menuconfig) compiled with it. All drivers selected as external modules (option [M] in menuconfig) need to be compiled and saved in the rootfs /lib directory. These are the commands to use to compile them:

```bash
# make modules compiles individual files for each question you answered M during kernel
# config. The object code is linked against your freshly built kernel. (For questions
# answered Y, these are already part of vmlinuz, and for questions answered N they are skipped).
make modules -j8 ARCH=arm CROSS_COMPILE=arm-linux-gnueabi-

# make modules_install copies your kernel modules to passed path e.g /modules/<kernel version>.
# Modules needs to be added to rootfs under /lib/modules/<kernel version> directory
make modules_install INSTALL_MOD_PATH=./modules ARCH=arm
```

#### 5. Reconfiguration

In case you want to re-configure the kernel from scratch and re-compile it, you must also issue a couple `make` commands that clean intermediate files. Note that `make mrproper` deletes your .config file. The complete process is:

```bash
make mrproper
make <config file>
make menuconfig
make clean
make
make modules
make modules_install
```

### Compiling Linux Kernel with Buildroot

Linux Kernel can also be compiled in the context of Buildroot. Process is much straight forward as all you need is to tell Buildroot URL of kernel source tree, URL of patch file and that's it. For more information see section with Buildroot description. 
