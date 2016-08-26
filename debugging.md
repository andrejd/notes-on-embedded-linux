## Remote debugging

#### Cross compiler setup

1. Install cross compiler for target platform, e.g. [Codesourcery - 2014.05](http://sourcery.mentor.com/public/gnu_toolchain/arm-none-linux-gnueabi/) to some folder, preferably to `/opt`
2. On 64-bit Linux machine some additional libraries needs to be installed before using toolchain:
```
sudo apt-get install lib32ncurses5 lib32z1
```
3. Add cross compiler path to $PATH, so it is available from any folder you like, e.g add to `.bashrc` next lines
```
export PATH=/opt/codesourcery/arm-2014.05/bin:$PATH
CROSS_COMPILE=arm-none-linux-gnueabi-
```
and then commit changes with `source .bashrc`. If all is ok, `arm-none-linux-gnueabi-gcc -v` should return cross compiler version.



#### Install Eclipse

Open a terminal window and add PPA repository maintained by webupd8.org, a popular Ubuntu Linux blog, which provides the most recent installers to automatically download and install Oracle Java JDK6 / JDK7 / JDK8 from oracle website.
```bash
sudo add-apt-repository ppa:webupd8team/java
# after that, update package lists via:
sudo apt-get update
# to install Oracle Java 8, run:
sudo apt-get install oracle-java8-installer
# change the number 8 to 6 (or 7) in the code to install Java 6 (or 7).
```

While installation, you’ll be asked to agree the license and then the installer start downloading Java file from oracle website and install it on your system.

To set the default Java, run:
```bash
sudo apt-get install oracle-java8-set-default
# finally check whether everyting is OK:
java -version
```

Next go to the link provided below and download the latest linux version of the Eclipse IDE installer.
```
http://www.eclipse.org/downloads/eclipse-packages/?osType=linux&release=undefined
 ```
Decompress the downloaded archive in your file browser and navigate to the result “eclipse-installer” folder, right-click on file eclipse-inst and select Run. When the wizard launches, select install item (e.g. C/C++ edition), then installation folder, and finally click INSTALL button.


###### Create a launcher shortcut

Open terminal and paste below command and run:
```bash
gedit ~/.local/share/applications/eclipse.desktop
#The command creates and opens a launcher file, for current user, with gedit text editor. Paste into it below content:

# depends on your eclipse installation folder, change corresponding paths below.
[Desktop Entry]
Name=Eclipse
Type=Application
Exec=/home/USERNAME/java-mars/eclipse/eclipse
Terminal=false
Icon=/home/USERNAME/java-mars/eclipse/icon.xpm
Comment=Integrated Development Environment
NoDisplay=false
Categories=Development;IDE;
Name[en]=Eclipse
```

#### Create Eclipse project

C/C++ project in eclipse has to be correctly configured with installed cross toolchain to work correctly. Here are settings for example project. You also get option to enter cross toolchain settings in new project wizzard.

![Debug configuration 1](/images/remote-debug-5.png)


#### Debugging on target

Target has to have `gdbserver` application installed on it. Eclipse knows how to run it via ssh connection without our help. For debugging to work, correct debug configuration in eclipse has to be created with remote target details specified.

###### Create debug configuration

To debug applications on remote device from eclipse you need to create correct debug configuration as seen on images below. On toolbar click on down arrow next to Debug button to access menu for creating Debug configurations.

![Debug configuration 1](/images/remote-debug-1.png)

![Debug configuration 4](/images/remote-debug-4.png)

![Debug configuration 2](/images/remote-debug-2.png)

![Debug configuration 3](/images/remote-debug-3.png)

To debug application, just select created configuration and eclipse should copy built application to target, start gdb server and initialize debugging perspective.
