## Linux-Unix - Hostname Related Items

By Jack Szwergold, September 27, 2015

### Checking the value of—and temporarily changing—hostname values.

This `hostname` command will give you the hostname connected to the system on the system:

    hostname

Using the same `hostname` command with the `--fqdn` option will show you the fully qualified domain name connected to your machine as returned by the DNS resolver. This might be 100% different from your machine’s locally defined hostname. But for many setups, the hostname should be the value in the `/etc/hosts` file:

    hostname --fqdn

And if you feel like changing the hostname value, set it by using `sudo` and assigning a value like this:

    sudo hostname example.com

That said, this `sudo hostname example.com` method is a temporary solution that will most likely get reset on reboot. So if you want to set the hostname value permanently, do the following.

### Permanently change the hostname value.

To permanently change the hostname on an Ubuntu/Debian system, open up `/etc/hostname` and change the value in there:

    sudo nano /etc/hostname

To permanently change the hostname on a CentOS/RedHat system, open up `/etc/sysconfig/network` like this:

    sudo nano /etc/sysconfig/network

Look for the `HOSTNAME` value and adjust it like this:

	NETWORKING=yes
	HOSTNAME="example.com"
	GATEWAY="192.168.56.1"
	GATEWAYDEV="eth1"
	FORWARD_IPV4="yes"

### Using `sysctl` to permanently change the hostname value.

You can also use `sysctl` to change the hostname. First check the hostname value like this:

    sysctl kernel.hostname

And if you feel like changing it, set it by using `sudo` and assigning a value like this:

    sudo sysctl kernel.hostname=example.com

***

*Linux-Unix - Hostname Related Items (c) by Jack Szwergold. This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*


