## Raspberry Pi Setup instructions

### Images for Raspberry Pi

Alternative to official [Raspbian](http://www.raspbian.org/) OS can be [MINIBIAN](https://minibianpi.wordpress.com/) which is stripped down version of Raspbian and fits to 512MB SD card. Its purpose is to be used as a headless server or for use in embedded projects.

To update & upgrade existing packages on Raspberry Pi before instalations of other software use these commands

```
sudo apt-get update
sudo apt-get upgrade
```


#### Topics covered in this README

- [SSH server](#ssh-server)
- [WIFI network](#wifi-network)
- [Samba](#samba)
- [SPI support](#spi-support)
- [Node.js](#nodejs)
- [Express.js](#expressjs)
- [NFS Shared folder](#setup-and-mount-nfs-shared-folder)

to be continued ...


### SSH server

SSH server can be started with raspi-config (http://elinux.org/RPi_raspi-config) which comes preinstalled on Whezzy.

  * To start raspi-config app type ```sudo raspi-config```

===
###WIFI network

You can access network interfaces on RPi with command ```sudo nano /etc/network/interfaces```. Content of file should be next (for DHCP configuration):

	auto lo

	iface lo inet loopback
	iface eth0 inet dhcp

	allow-hotplug wlan0
	iface wlan0 inet dhcp
	wpa-conf /etc/wpa_supplicant/wpa_supplicant.conf
	iface default inet dhcp

Wireless settings are stored in file *wpa_supplicant.conf*:
```sudo nano /etc/wpa_supplicant/wpa_supplicant.conf```
Content of *wpa_supplicant.conf* should be:

	ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
	update_config=1

	network={
	ssid="SSID_NAME"
	psk="SECRET_PASSWORD"

	# Protocol type can be: RSN (for WP2) and WPA (for WPA1)
	proto=RSN

	# Key management type can be: WPA-PSK or WPA-EAP (Pre-Shared or Enterprise)
	key_mgmt=WPA-PSK

	# Pairwise can be CCMP or TKIP (for WPA2 or WPA1)
	pairwise=CCMP TKIP

	#Authorization option should be OPEN for both WPA1/WPA2 (in less commonly used are SHARED and LE$
	auth_alg=OPEN
	}


===
###Samba


First we need to install samba:
```sudo apt-get install samba samba-common-bin```

We need to create users, e.g. user 'pi' (users are stored in separate db than linux users):
```sudo smbpasswd -a pi```

We also need to configure Samba and let it know which folders can be shared:
```sudo nano /etc/samba/smb.conf```

Contents of *smb.conf*:

```
[global]
    workgroup = WORKGROUP
    #usershare allow guests = yes
    #security=share
    security=user
    follow symlinks = yes
    wide links = no
    unix extensions = no
    lock directory = /var/cache/samba
    socket options = TCP_NODELAY
[pi]
    browsable = yes
    read only = no
    #guest ok = yes
    valid users = pi
    path = /home/pi
    #force user = pi (no longer needed)
[devices]
    browsable = yes
    read only = no
    #guest ok = yes
    valid users = pi
    path = /media
    force user = root
```

We can then manipulate with Samba service

```sudo /etc/init.d/samba restart [stop][start]```

Misc notes about Samba:

  * Add option under [global] ```socket options = TCP_NODELAY``` to boost samba performance


===
### SPI support


SPI module is disabled by default in RPi, you have to enable it in ```raspi-blacklist.conf``` file by putting ```#``` before spi-bcm2708 entry

```
  	$ sudo nano /etc/modprobe.d/raspi-blacklist.conf
```

File should have this entry:
	```#spi-bcm2708```

Reboot to take effect

#### Installation of C library which will be used to interface SPI
```
	$ sudo apt-get update
	$ sudo apt-get install automake libtool git
	$ git clone https://github.com/thomasmacpherson/piface.git
	$ cd piface/c
	$ ./autogen.sh && ./configure && make && sudo make install
	$ sudo ldconfig
	$ cd ../scripts
	$ sudo ./spidev-setup
```

After running upper commands this was written out:
```
  	----------------------------------------------------------------------
	Libraries have been installed in:
	   /usr/local/lib

	If you ever happen to want to link against installed libraries
	in a given directory, LIBDIR, you must either use libtool, and
	specify the full pathname of the library, or use the `-LLIBDIR'
	flag during linking and do at least one of the following:
	   - add LIBDIR to the `LD_LIBRARY_PATH' environment variable
	     during execution
	   - add LIBDIR to the `LD_RUN_PATH' environment variable
	     during linking
	   - use the `-Wl,-rpath -Wl,LIBDIR' linker flag
	   - have your system administrator add LIBDIR to `/etc/ld.so.conf'

	See any operating system documentation about shared libraries for
	more information, such as the ld(1) and ld.so(8) manual pages.
	----------------------------------------------------------------------
	 /bin/mkdir -p '/usr/local/include/libpiface-1.0'
	 /usr/bin/install -c -m 644 src/piface/pfio.h '/usr/local/include/libpiface-1.0'
	 /bin/mkdir -p '/usr/local/lib/pkgconfig'
	 /usr/bin/install -c -m 644 piface-1.0.pc '/usr/local/lib/pkgconfig'
	make[1]: Leaving directory `/home/pi/piface/c'
```

Last two commands when installing C library added user 'pi' to group spiuser so we can use device under ```/dev``` folder without being sudo. Output from script:
```
		Creating udev rule
		Adding user pi to group spiuser
		Adding user www-data to group spiuser
		User "pi" can now access the /dev/spidev* devices
		Please REBOOT for the changes to take effect
```

===
### Node.js


Install Node.js to RPi with following commands (you can change Node version to latest, if desired):
```
	$ wget http://nodejs.org/dist/v0.10.22/node-v0.10.22-linux-arm-pi.tar.gz
	$ tar -zxvf node-v0.10.22-linux-arm-pi.tar.gz
	$ sudo mkdir /opt/node
	$ sudo cp -r node-v0.10.22-linux-arm-pi/* /opt/node
```

Adding Node.js to PATH: Open ```$ sudo nano /etc/profile``` and add before export command:
```
  	NODE_JS_HOME="/opt/node"
	PATH="$PATH:$NODE_JS_HOME/bin"
```

When installing modules globally, you need to use ```sudo -i npm install -g [module name]```so that ```$PATH``` is not replaced with default ```$PATH``` of super user (in which there is no entry for npm) - or you have to use absolute path to npm binaries - e.g. ```/opt/node/npm``` ...

> Node v0.11.9 was also used on RPi, but it didn't work. Module installation returned some errors and no foders were created inside node_modules dir.

Alternative option to install Node.js is by following rules on http://node-arm.herokuapp.com/ where you can find debian package which is then installed via `dpkg`


#### Express.js

Express.js is a Node module and should be installed globally so we can use it as command to generate application skeletons with ```express [cmd]```)

#### Setup and mount NFS shared folder

Sometimes it is useful to extend limited RPi SD card space with larger, NFS shared folder:
- RPi has access to shared network storage
- development of RPi projects can be done on desktop PC with favorite editor and same source code can be accessed directly on RPi. Useful for developing projects where compilation needs to be done on RPi (Node.js, C++, ... projects)

##### 1. Setting up the server

First you need to pick a directory on the server which you want to use as shared space. Here we are going to use `/srv/public`
```
sudo mkdir -p /srv/public
sudo chmod 777 /srv/public
```
Next we need to install the NFS server.
```
sudo apt-get install nfs-kernel-server nfs-common portmap
```
Next you need to configure nfs by adding the directory to `/etc/exports`
```
sudo vi /etc/exports
```
and add the following line to the end of the file:
```
/srv/public 192.168.1.0/24(rw,sync,no_subtree_check)
```
Here we declare that the `/srv/public` directory is Read Write (rw) and it’s accessible by any machine within the local network – 192.168.1.x in this instance. Lastly we need to restart _nfs-kernel-server_ to apply settings.
```
sudo service nfs-kernel-server restart
```

##### 2. Setting up Raspberry Pi

Now we are going to mount this share on the RPi. First you need to install NFS. If you are using the standard Debian image then you have enough to mount nfs. If in doubt then simply install it as we did for the server – if it’s already there it will just tell you it’s already installed. Next decide where to mount it. Now you can mount it anywhere you want but I’m going to use the same `/srv/public` location – an alternative location for example is `/mnt/public`.

First make the directory as you did on the server.
```
sudo mkdir -p /srv/public
sudo chmod 777 /srv/public
```
Next we’ll mount the share:
```
sudo mount <YOUR_PC_NAME>:/srv/public /srv/public
```
If you want to unmount the drive then it’s simply a case of:
```
sudo umount /srv/public
```
To mount the drive on boot you need to add entry to `/etc/fstab`:
```
<YOUR_PC_NAME>:/srv/public /srv/public nfs defaults 0 0
```
