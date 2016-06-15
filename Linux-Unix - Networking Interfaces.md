## Linux-Unix - Networking Interfaces

By Jack Szwergold

***

### Manually start a network interface.

Run this command to manually bring up a network interface via `ifconfig`; mainly useful during initial system setup to get an interface up from the console:

	sudo ifconfig eth1 192.168.56.10 netmask 255.255.255.0 up

While this works, these settings are lost on reboot. To retain network settings on reboot, follow the instructions below.

### Manually restarting all network interfaces.

Some places saying restarting the `networking` service is the best way to restart network interfaces:

    sudo service networking restart

But in Ubuntu 12.04.5 the response returned is something like this:

	stop: Unknown instance: 
	networking stop/waiting

Instead, running this combination of `ifdown` and `ifup` seems to work more reliably while still respecting the order of items in `/etc/network/interfaces`:

    sudo ifdown --exclude=lo -a && sudo ifup --exclude=lo -a

### Automatically start a network interface on boot.

You can then add a network interface by opening up `/etc/network/interfaces`:

	sudo nano /etc/network/interfaces

And then add a chunk of configuration code like this; adjust for your specific needs. This is for a static IP address:

	auto eth0
	iface eth0 inet static
	address 192.168.1.160
	netmask 255.255.255.0
	gateway 192.168.1.12
	dns-nameservers 192.168.1.10 192.168.1.20 192.168.1.30
	dns-domain something.com
	dns-search something.com

If you need to use a DHCP/NAT-based IP address, just use this:

	auto eth0
	iface eth0 inet dhcp

### Clearing up MAC address issues.

If the MAC address of an interface in the OS does not match the MAC address of the actual interface, networking will choke.

This would mainly come up in VirtualBox when a hard drive for one virtual machine is used within another, new virtual machine. Or maybe even transfering a hard drive with the Linux OS from one machine to another. Basically, MAC addresses change and things might get weird.

To fix it, just edit this file to get change the MAC address to match the new setup:

	sudo nano /etc/udev/rules.d/70-persistent-net.rules

Or more simply, just toss the file entirely and reboot the virtual machine; the `70-persistent-net.rules` will be regenerated after reboot:

	sudo rm /etc/udev/rules.d/70-persistent-net.rules

### Flushing the routing table.

Sometimes you need to flush the routing table to clear up some networking issues. 

Do this to view the current routing table entries:

    netstat -nr

Now, take down the main network interface on the machine like this:

	sudo ifconfig eth0 down

The run, this `route flush` command to flush the routing tables:

	sudo route flush

Check the routing tables again if you wish:

    netstat -nr

They should be set to some default state. All good? Now startup the main network interface again like this:

	sudo ifconfig eth0 up

***

*Linux-Unix - Networking Interfaces (c) by Jack Szwergold; written September 11, 2015. This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*