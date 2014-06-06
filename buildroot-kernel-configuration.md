## Buildroot kernel configuration


To use existing Linux kernel, you can point Buildroot to that file

    Kernel -> Linux Kernel -> Kernel Version -> Custom tarball
and set path to your custom tarball as 

    file://$(HOME)/Development/Linux/linux-2.6.39.tar.bz2`

You also need to provide kernel configuration file name (omit *_defconfig* part of file name): 
    
    at91sam9x5ek 
for example.

