## Misc configuration flags for Linux Kernel

### Reboot, shutdown on AT91 boards

To enable commands `rebot`, `shutdown` in linux console, we need to enable CONFIG_POWER_RESET_AT91_POWEROFF and CONFIG_POWER_RESET_AT91_RESET:

```
Device Drivers -> Power supply class -> Board level reset or power off
```

### Kernel task switching frequency

To set frequency of task switching select desired value (default is 100Hz)

```
Kernel Features -> Timer frequency
```

### Wifi drivers

Select desired wifi drivers:
```
Device Drivers -> Network device support -> Wireless LAN
```
Additional drivers are also found under
```
Device Drivers > Staging drivers
```

### Watchdog

Watchdog has to be enabled in multiple places.

###### AT91Bootstrap
By default the watchdog is disabled at boot time by AT91Bootstrap. To enable it you have to recompile AT91Bootstrap after have disabled this flag:

```
[ ] Disable Watchdog   
```

###### U-Boot

U-Boot has in source tree support for watchdogs (`drivers/watchdog/`) where are located drivers for different platforms. Atmel IC's related driver is found in file `at91sam9_wdt.c`.
Watchdog is not enabled by default, you need to modify configuration header file for your board located in `include/configs/` directory of U-Boot source tree.

To enable watchdog for Acme Acqua SoM, one has to modify file `acqua.h` (board) file in `include/configs` directory by adding two defines and recompile source tree to get new binaries:
```c
#define CONFIG_HW_WATCHDOG
#define CONFIG_AT91SAM9_WATCHDOG
```

> If watchdog has already been configured before, e.g. in AT91Bootstrap, you also need to modify `at91sam9_wdt.c` file by commenting out
> watchdog initialization code, as for security reasons watchdog on Atmel IC's can be configured only once.

> With Watchdog enabled and SPL option checked in U-Boot configuration compilation of SPL bootloader will fail.  As we do not need SPL, we can disable it in U-Boot settings.

>*"The point with an SPL is to create a very small preloader, which loads the 'full' U-Boot image. It’s built from U-Boot’s sources, but with a minimal set of code. So when U-Boot is built for a platform that requires SPL, it’s typically done twice: Once for generating the SPL, and a second time for the full U-Boot. The SPL build is done with the CONFIG\_SPL defined. Only the pre-location phase runs on SPL builds. All it does is the minimal set of initializations, then loads the full U-Boot image, and passes control to it."*

###### Kernel
This is the Kernel flag to enable inside the Kernel configuration:
```
Device Drivers -> Watchdog Timer Support -> AT91SAM9X / AT91CAP9 watchdog
```

You also need to add in board `.dts` these lines inside the apb section:
```
apb {

        ...

        watchdog@fffffe40 {
            compatible = "atmel,at91sam9260-wdt";
            reg = <0xfffffe40 0x10>;
            timeout-sec = <30>;
            status = "okay";
        };

        ...
    };
  ```
