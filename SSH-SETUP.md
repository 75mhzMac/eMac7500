# eMac (Radeon 7500 Graphics) - 3x OS Install with OpenBSD
### SSH Setup

![eMac with OpenBSD](pufferfish.png)

## Please Read Before Proceeding

### Knowing Your Exact eMac Model

This has only been tested on a 2003 1GHz eMac with Radeon Graphics. 
The 800MHz ATI model is nearly identical, so the display configuration should work just fine with this eMac.
This xorg.conf was frankensteined (to say the best) using a Debian config file, and ActionRetro's xorg.conf for running OpenBSD on a G3 iMac.

To my dumb luck, it worked!

My knowledge with Xorg display configurations is limited.
Any help in regards to getting other eMac models to run OpenBSD would surely be appreciated by all.

The configuration sets the internal display to 1280x960 (it's maximum, which is fine)
However, it would be nice if OpenBSD could change resolutions.

### Warning

This display configuration should be used **AT YOUR OWN RISK**, and it should be noted to edit this file with caution.

From my understanding, **an out-of-range refresh rate could potentially damage your CRT monitor.**

#### For Reference Only
Original Debian xorg.conf file for the 1GHz eMac:
- https://web.archive.org/web/20170505053827/mac.linux.be/content/g4-emac-1ghz-radeon

ActionRetro's xorg.conf file for G3 iMacs:
- http://frogfind.com/files/bsd/xorg.conf

### Thank You
A very special THANK YOU to Action Retro and DistroHopper39B for their help in making this all possible.
- https://www.youtube.com/c/ActionRetro
- https://www.youtube.com/c/distrohopper39b

Be sure to subscribe to their channels for wonderful videos on PowerPC Macs and other retro electronics!

## Requirements
- 800MHz or 1 GHz eMac with Radeon 7500 Graphics
- 512MB RAM or higher recommended
- Mac OS 9 for Unsupported G4s CD (Macintosh Garden) 
  - https://macintoshgarden.org/apps/mac-os-922-install-unsupported-g4s
- Mac OS X Install DVD
- OpenBSD macppc 7.1 ISO written to USB flash drive
  - https://cdn.openbsd.org/pub/OpenBSD/snapshots/macppc/install71.iso 
- Ethernet internet connection
- Second Computer with Terminal/SSH

### Setup Using External Display
Refer to the file [README.md](README.md) for instructions on setting up OpenBSD using a VGA adapter and display.

## Preparing Your Hard Disk

1. Start your computer using the Mac OS 9 for Unsupported G4s CD. 
2. Open the Drive Setup application located on your installer disc.
3. Choose the internal ATA hard drive and click the `Initialize` button.
4. Click the `Custom Setup...` button.
5. Create 3 partitions. 1 for each OS. The first two are left as Mac OS Extended.
6. Click on the partition labelled untitled 3 and change the type to `Unallocated`.
7. Be sure to size your partitions according to your personal use. (for example: 14G Mac OS 9, 20G Mac OS X, 5GB OpenBSD)
8. Mac OS 9 should be installed on the first partition.
9. Click the `OK` button to apply your settings, then click the `Initialize` button to write your changes to disk.
10. Rename the volume untitled2 to "Mac OS X" or to your preference.


## Install Mac OS 9
 
1. Open the Apple Software Restore application from the MacOS9Lives CD located on your desktop.
2. Click on the `Switch Disk` button until the disk labeled "untitled" is chosen, if it isn't already.
3. Click `Restore` to begin. Click `OK` to confirm.
4. The disk will be renamed to "MacOS9Lives" upon completion.


## Copying Boot File for OpenBSD
1. Restart the computer and hold down the `Option` key.
2. Make sure the MacOS9Lives volume is selected and press the button with the right arrow.
3. Wait for the operating system to boot.
4. When finished, the setup program will launch.
5. Press `Command`+`Q` to skip the setup process.
6. Insert your OpenBSD USB installer disk.
7. At the root of the disk is a file named `ofwboot`. 
8. Copy this file to the root of your MacOS9Lives partition.

## Take Note of IP Address
- Ensure your eMac is now connected to the internet via Ethernet. 
- Open the `TCP/IP` Control Panel from the Apple menu.
- Your IP Address will be found here. Take note of this address as you will need it to setup OpenBSD.

## Install Mac OS X

#### Note For Installing Sorbet Leopard Revision 1.5 from External Hard Disk
I have noticed that the partition is missing from the Startup Manager after installing using Carbon Copy Cloner.
Repairing the disk using Disk Utility and choosing it as a startup disk in System Preferences remedied the issue.

1. Begin by starting up from your OS X installation disc. Choose your language.
2. Open Disk Utility from the Utilities menu.
3. Select the partition labeled "Mac OS X" then click on the `Erase` tab.
4. Change the Volume Format to "Mac OS Extended (Journaled)"
5. Click the `Erase` button. Click `Erase` again to confirm.
6. Quit Disk Utility
7. Continue installation, choosing your Mac OS X disk as the destination.
8. Choose `Custom Install` to customize.
9. Proceed with installation.

#### Note

The installer disk will automatically choose your new installation as the default operating system to boot.

### Mac OS X Setup

The installer will automatically restart once it has finished. 

1. Setup your Mac OS X installation.
2. Once complete, eject the Mac OS X Install DVD.
3. Insert the Mac OS 9 Installer CD. You will need to boot from this later.
4. Insert your OpenBSD installation disk directly to the eMac (not using a USB hub or the keyboard)

## Install OpenBSD 7.1

- Power on your computer.
Type the following into the Open Firmware prompt:

`Command`+`Option`+`O`+`F`
```
dev usb0
ls
```
- You will now see a list of available devices. What you are looking for is `disk@`. 
- If you have found your disk in the list, take note of the number your disk is located on.

If no disks are found, examine the next USB port with the following command.
```
dev usb1
ls
```

Let's say the USB disk is located at usb1 on `disk@1`.

``` 
boot usb1/disk@1:,ofwboot 7.1/macppc/bsd.rd
```
- Eventually you will be greeted with the OpenBSD installer. 
- Press the `i` key, then `return` to begin the installation.

```
Enter your hostname. [eMac]
Which network interface do you wish to configure? [gem0]
IPv4 address for gem0? [autoconf]
IPv6 address for gem0? [none]
Which network interface do you wish to configure? [done]

Enter root account password.
Start ssh(8) by default [yes]
Do you want the X Window System to be started by xenodm(1)? [no]
Change the default console to zstty0? [no]
Setup a user? (Enter a username)
Password? 
Allow root ssh login? [no]
Timezone? (Press ? if not correct).

Which disk is the root disk? [wd0]
Use HFS or MBR partition table? [hfs]
Modify or abort? [Modify]
```

### Modify The Apple Partition Map
Note where your unformatted partition space is located. It should be number 11 on the list.
```
Command: p 
Command: t
Partition number: 11 
New type of partition: OpenBSD
Command: n
Partition number: 11
New name of partition: OpenBSD
Command: p
Command: w
Writing the map destroys what was there before. Is that OK? y
Command: q


Use (A)uto layout, (E)dit auto layout, or create (C)ustom layout? [a]

Which disk do you want to initialize? [done]
```

### Download OpenBSD sets to HD 
It is OK to use whatever server is automatically suggested.
```
Location of sets? [http]
Use proxy URL? [none]
HTTP Server? [cdn.openbsd.org]
Server directory? [pub/OpenBSD/snapshots/macppc/]

Set name(s)? [done]
```

OpenBSD will now proceed to install onto your hard drive.

```
Location of sets? [done]
Exit to shell? [reboot]
```
#### Important!
- **Here is where inserting the Mac OS 9 installation CD comes in.**
- **Hold down the `Option` key as soon as the computer restarts for the next step!**

## Fix Disk Drivers Broken During Installation
Upon completion of installing OpenBSD, will you will need to update the Mac OS 9 Driver. This happens when OpenBSD makes changes to the Apple Partition Map.

1. Boot from the MacOS9Lives installation disk.
2. Open the Drive Setup application. 
3. Select `<not mounted>` ATA disk
4. Under the Functions menu, choose Update Driver
5. Restart

You will now be able to boot again from your OS 9 partition.

#### Note
If for some reason you need to reinstall OpenBSD, you will not need to repeat this step again.

## Boot Management
Choose which operating system your computer will use during startup.

### OpenBSD as default OS on boot
You can set OpenBSD to be the default OS at startup, and use the option key to choose between Mac OS versions.

`Command`+`Option`+`O`+`F` 
```
setenv boot-device hd:,ofwboot \bsd
reset-all
```

### Apple OS as default
- Choose a partition using the Startup Disk control panel on Mac OS 9/X. 
- Recommended when another operating system such as Mac OS 9 is the most frequently used.

### Boot OpenBSD from Open Firmware prompt
`Command`+`Option`+`O`+`F`
```
boot hd:,ofwboot \bsd
```
The Open Firmware prompt can also be accessed from the Startup Manager by pressing `Control`+`Z`


## OpenBSD Setup
1. Boot into your OpenBSD installation.
2. After a minute or so, you should be able to log into your eMac from another machine.

Let's say `192.168.1.24` is your eMac's IP address and `megan` is the username chosen during installation.
```
ssh megan@192.168.1.24
```
- If prompted, type in `yes` and press the `Return` key.
- Type in your password and press `Return`.

You are now logged into your eMac. You can now proceed with the setup process.

#### Tip
If your keyboard cannot type a tilde, then replace `~/` with `/home/yourownusername/`.
The `~/` is just a shortcut to your home folder.

## Install Nano & Window Manager

### For IceWM only (recommended for first time users)
```
su
pkg_add nano icewm git
exit
nano ~/.xsession
```
Type the following into the text editor.
```
exec icewm
```
- `Control`+`o`
- `return` to save changes
- `Control`+`x` to exit

**Skip the next step and proceed to copying over the Xorg configuration file.**

### For Mac-Like Virtual Window Manager + IceWM
```
su
pkg_add nano icewm mlvwm git
exit
git clone https://github.com/morgant/mlvwmrc.git
cd ~/mlvwmrc
make && make install
nano ~/.xsession
```

- Type the following into the text editor.
- Use this file to switch between window managers, replacing `mlvwm` with `icewm`.

```
exec mlvwm
```
- `Control`+`o`
- `return` to save changes
- `Control`+`x` to exit



## Copying Over The Xorg.conf File
Be sure to replace `username` with your own actual user name you created during installation.
```
git clone https://github.com/75mhzMac/eMac7500.git
su
cp /home/username/eMac7500/xorg.conf /etc/X11/
```

## Enable Xenodm
This allows the window manager to run at startup.
```
rcctl enable xenodm
```


## Suggested Setup (Optional)
Replace `username` with the one provided during installation.
```
usermod -G operator username
usermod -G staff username
usermod -G wheel username
nano /etc/doas.conf
```
Enter the following into the text editor:
```
permit persist keepenv :wheel
```
- `Control`+`o`
- `return` to save changes
- `Control`+`x` to exit

You can now use doas instead of having to enter/exit su, similar to sudo in Linux.

**The screen will go blank during a good part of the boot process, until the xorg.conf has been loaded.**

## Command to Restart The Computer
```
shutdown -r now
```
Your eMac will now restart and your SSH session will disconnect.

#### Command to Power Off The Computer
```
shutdown -p now
```

# Setup Completed
Take a well deserved break.


## OpenBSD Extras

### MLVWM Fix / Hide XConsole
- A white bar will appear near the bottom of the screen when using MLVWM. This will also hide XConsole at startup on any Window Manager.
```
doas nano /etc/X11/xenodm/Xsetup_0
```
- Comment out line pertaining to xconsole by adding # to the beginning of the line.


### Change The Login Screen Background Color
- Doesn't apply to MLVWM session. See https://github.com/morgant/mlvwmrc for more info on configuration.
```
doas nano /etc/X11/xenodm/Xsetup_0
```
Add this line to the end of the file.
```
xsetroot -solid "#79a0d9"
```
Change the hex color to your liking.

## Adding Software

### Browse Packages

https://cdn.openbsd.org/pub/OpenBSD/7.1/packages/powerpc/

#### Recommended Software
- htop
- neofetch
- tigervnc
- dillo
- netsurf
- lynx
- emacs

Yes, `emacs` running on an eMac.

#### Check The Weather
Use the `curl` command in your terminal for a quick 3-day forecast. Use a zip code or city name.
```
curl wttr.in/94601
```

## Raspberry Pi owners
Install tigervnc to connect to your pi to handle web browsing with RealVNC server enabled.


