## U-Boot configuration


U-Boot can be used among other things also to supply Linux kernel with custom command line which can be helpful to change kernel behavior without recompilation.

### Basic U-Boot usage

From U-Boot console type:
- `printenv`: displays current environment variables, you can see cmd line that will be passed to kernel
- `setenv`: sets new env variables; not stored in flash and they will be lost upon reboot, you can save them with `saveenv` command
- `saveenv`: save environment variables to persistent storage

### Set `bootdelay`

Change delay before U-Boot boots into kernel (here is set to 3 seconds):

    setenv bootdelay 3
    saveenv


 





### NAND flash partitioning

Change how Linux kernel sees NAND flash by defining MTD partitions in the command line.
> Atmel boards usually have two flash devices on board; one smaller serial flash to store AT91Bootstrap (1st level bootloader) and U-Boot bootloader and big NAND flash device for kernel and rootfs. This layout is usefull for system recovery (if e.g. NAND flash goes wacky, you can still access it through U-Boot).   


    setenv bootargs console=ttyS6,115200 mtdparts=atmel_nand:6M(kernel)ro,250M(rootfs) rootfstype=ubifs ubi.mtd=1 root=ubi0:rootfs
    saveenv
 
Partitions are defined with `mtdparts` parameter in the command line. From example above we have defined two partitions on 256MB flash device: 6 MB for Linux kernel and remaining 250 MB for root file system.

> Other way to define MTD partitions is to modify kernel source files




