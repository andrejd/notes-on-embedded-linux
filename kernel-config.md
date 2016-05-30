## Misc configuration flags for Linux Kernel

#### Reboot, shutdown on AT91 boards

To enable commands `rebot`, `shutdown` in linux console, we need to enable CONFIG_POWER_RESET_AT91_POWEROFF and CONFIG_POWER_RESET_AT91_RESET:

- *Device Drivers/Power supply class/Board level reset or power off*

#### Watchdog

to enable kernel support for Atmel watchdog,
