# Cheat Sheet - VirtualBox

By Jack Szwergold, September 19, 2015

#### Start the VirtualBox virtual machine as a headless machine.

    nohup VBoxHeadless --startvm "Sandbox" &

Slight variant of the command without a `nohup.out` file being created:

    nohup VBoxHeadless --startvm "Sandbox" >/dev/null 2>&1 &

#### Control the VirtualBox virtual machine state from the command line.

    VBoxManage controlvm "Sandbox" pause
    VBoxManage controlvm "Sandbox" resume
    VBoxManage controlvm "Sandbox" reset
    VBoxManage controlvm "Sandbox" poweroff
    VBoxManage controlvm "Sandbox" savestate
    VBoxManage controlvm "Sandbox" acpipowerbutton
    VBoxManage controlvm "Sandbox" acpisleepbutton

### Sundry networking stuff.

#### Some basic VirtualBox networking settings.

- **Sandbox IP:** 192.168.56.10
- **Router:** 192.168.56.1
- **Netmask:** 255.255.255.0

#### Temporarilly add a network interface.

When the machine initially starts up you can temporarilly setup a new interface with this `ifconfig` command:

	sudo ifconfig eth1 192.168.56.10 netmask 255.255.255.0 up

#### Permanently add a network interface.

Open up the `/etc/network/interfaces` file:

	sudo nano /etc/network/interfaces

And add—or adjust—the interface details like this:

	# The local hostmachine access interface.
	auto eth1
	iface eth1 inet static
	address 192.168.56.10
	netmask 255.255.255.0

#### Sample `ifconfig` output for reference.

	eth0      Link encap:Ethernet  HWaddr 08:00:27:17:16:01
	          inet addr:10.0.2.15  Bcast:10.0.2.255  Mask:255.255.255.0
	          inet6 addr: fe80::a00:27ff:fe17:1601/64 Scope:Link
	          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
	          RX packets:225 errors:0 dropped:0 overruns:0 frame:0
	          TX packets:265 errors:0 dropped:0 overruns:0 carrier:0
	          collisions:0 txqueuelen:1000
	          RX bytes:24343 (24.3 KB)  TX bytes:26990 (26.9 KB)
	
	eth1      Link encap:Ethernet  HWaddr 08:00:27:e7:29:74
	          inet addr:192.168.56.10  Bcast:192.168.56.255  Mask:255.255.255.0
	          inet6 addr: fe80::a00:27ff:fee7:2974/64 Scope:Link
	          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
	          RX packets:56 errors:0 dropped:0 overruns:0 frame:0
	          TX packets:82 errors:0 dropped:0 overruns:0 carrier:0
	          collisions:0 txqueuelen:1000
	          RX bytes:7430 (7.4 KB)  TX bytes:13696 (13.6 KB)
	
	lo        Link encap:Local Loopback
	          inet addr:127.0.0.1  Mask:255.0.0.0
	          inet6 addr: ::1/128 Scope:Host
	          UP LOOPBACK RUNNING  MTU:65536  Metric:1
	          RX packets:2 errors:0 dropped:0 overruns:0 frame:0
	          TX packets:2 errors:0 dropped:0 overruns:0 carrier:0
	          collisions:0 txqueuelen:0
	          RX bytes:80 (80.0 B)  TX bytes:80 (80.0 B)

#### Install Avahi daemon for easy access to the guest OS.

Install the Avahi daemon:

    sudo aptitude install avahi-daemon avahi-utils

Edit the Avahi daemon config:

    sudo nano /etc/avahi/avahi-daemon.conf

Browse all Avahi services on the LAN:

    avahi-browse -at

Browse for broadcast services in Mac OS X:

    dns-sd -B

### Sundry configuration items.

#### Set the GRUB timeout to 0 to allow for a quick, headless boot and recovery.

Open up the `/etc/default/grub` file:

    sudo nano /etc/default/grub

Now look for the config line that looks like this:

    GRUB_TIMEOUT=2

And edit it to be something like this; this config basically sets the `GRUB_TIMEOUT` and `GRUB_RECORDFAIL_TIMEOUT` to `0` so the machine just boots up right away:

	#GRUB_TIMEOUT=2
	GRUB_TIMEOUT=0
	GRUB_RECORDFAIL_TIMEOUT=$GRUB_TIMEOUT

Save the file and update GRUB like this:

	sudo update-grub2

And all should be set.

#### Edit the 'sources.list' to enable partner package updates.

The main Ubuntu repository maintained by Canonical has lots of the sundry Linux stuff one needs, the `partner` repository has additional stuff Canonical cannot technically offer by default, but every Ubuntu user can access by doing the following:

	sudo nano /etc/apt/sources.list

Uncomment the following `partner` lines to add Canonical’s `partner` repository:

	# deb http://archive.canonical.com/ubuntu/ natty partner
	# deb-src http://archive.canonical.com/ubuntu/ natty partner

With that done, save the file and then run `aptitude` update like this:

    sudo aptitude update

And then do an `aptitude upgrade` to get everything evened out:
	
	sudo aptitude upgrade

### Sundry debugging items.

#### Clearing up MAC address issues.

If the MAC address of an interface in the OS does not match the MAC address of the actual interface, networing will choke.

This would mainly come up in VirtualBox when a hard drive for one virtual machine is used within another, new virtual machine. Or maybe even transfering a hard drive with the Linux OS from one machine to another. Basically, MAC addresses chnage and things might get weird.

To fix it, just edit this file to get change the MAC address to match the new setup:

	sudo nano /etc/udev/rules.d/70-persistent-net.rules

Or more simply, just toss the file entrirely and reboot the virtual machine; the `70-persistent-net.rules` will be regenerated after reboot:

	sudo rm /etc/udev/rules.d/70-persistent-net.rules

#### Clearing up UUID related issues.

If you get a UUID related error like this:

> Failed to open the hard disk <FilePath>.
>
> Cannot register the hard disk <FilePath><GUID> becuase a hard disk
> <DifferentFilePath> with UUID <GUID> already exists.

Go into the directory of your virtual machine; of course change the actual path to match the actual path you are going into:

    cd /full/path/to/virtualbox/virtualmachine/Sandbox

And run this command to assign the disk a new UUID:

    VBoxManage internalcommands sethduuid Sandbox.vdi

### Sundry VirtuaBox disk management stuff.

Create a new HD with 30,000 the max size in megabytes:

    VBoxManage createhd –-filename NewDisk.vdi --size 30000 --remember

Resize a  HD with 81,920 the max size in megabytes: 

    VBoxManage modifyhd Disk.vdi –resize 81920

Clone a VirtualBox hard disk:

    VBoxManage clonehd OldDisk.vdi NewDisk.vdi --existing

Compact a VirtualBox hard disk:

    VBoxManage modifyhd --compact Disk.vdi

***

*Cheat Sheet - VirtualBox (c) by Jack Szwergold*

*This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*