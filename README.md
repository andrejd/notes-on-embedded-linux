## Random notes on Embedded Linux

Collection of bits and pieces regarding embeded linux, what it is, how to build it, ....

#### Create your own Linux distribution


[Notes from StackOverflow](http://stackoverflow.com/questions/22255466/angstrom-archlinux-yocto-buildroot-choose-os-forembeded-x86/22267616#22267616)

>**[Buildroot](http://buildroot.uclibc.org/)**, **[Yocto](https://www.yoctoproject.org/)** and **[Ångström](http://www.angstrom-distribution.org/)** are embedded Linux distros, where you download the build system and then configure it for your target (select toolchain, packages etc.) and then build your bootloader, kernel and root file system.
**Yocto** and **Angstrom** are based on **OpenEmbedded** project. Buildroot has another approach and uses Kconfig system like Linux kernel.
>
>So far I have used only Buildroot for ARM systems and am very comfortable with this distro. It is very easy to create and extend your first rootfs. But you'll probably have to try some of these distros to understand, which one suites your needs. Look at distro's documentation, tutorials etc.
>
>If you don't want to compile your rootfs, try ArchLinux. If you are familiar with OpenEmbedded look at Yocto and Angstrom. If you like Kconfig try Buildroot.


#### Resources

- [Free Electrons](http://free-electrons.com/), lots of resources about embedded linux: pdf slides ...
- [AcmeSystems](http://www.acmesystems.it/), producers of Acqua SoM (System on Module)
