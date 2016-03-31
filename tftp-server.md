## TFTP server

If you need a TFTP Server up and running in Ubuntu and try to google it, you will find plenty of advices. Some of them are too old to work with current Ubuntu versions, so here is a guide for Ubuntu 12.04 LTS a.k.a Precise Pangolin.

#### 1. Install tftpd-hpa and openbsd-inet

I’ve chosen tftp-hpa as TFTP Server and openbsd-inetd as service to run it. Use

```bash
sudo apt-get install tftpd-hpa openbsd-inetd
```

to install these files. Change to `/var/lib/` afterwards and give rights to everyone to use the tftpboot directory (you can choose a different directory, which demands a change in the tftpd-hpa config file later on)

```bash
sudo chmod 777 tftpboot
sudo chown nobody:nogroup tftpboot
```

#### 2. Edit TFTP config file

Open the config file of tftpd-hpa:
```bash
cd /etc/default
sudo nano tftpd-hpa
```

Insert the following code (change the TFTP_DIRECTORY to the one you want to use):
```bash
# /etc/default/tftpd-hpa
RUN_DAEMON="yes"
TFTP_USERNAME="tftp"
TFTP_DIRECTORY="/var/lib/tftpboot"
TFTP_ADDRESS="0.0.0.0:69"
TFTP_OPTIONS="--secure"
```

#### 3. Restart Services

Finally, the new configs need to be used, so restart `openbsd-inetd` and `tftpd-hpa`:

```bash
sudo service openbsd-inetd restart
sudo service tftpd-hpa restart
```

You should be good to go!

UPDATE: Unfortunately there seems to be a bug in Ubuntu 12.04 that prevents tftp to be started at boot. You can either manually restart tftpd-hpa after each boot by entering “sudo service tftpd-hpa restart” or follow the instructions of post #30 in the launchpad bug entry:
https://bugs.launchpad.net/ubuntu/+source/tftp-hpa/+bug/522509
