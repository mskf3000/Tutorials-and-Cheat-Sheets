## Mac OS X - SNMP Basics

By Jack Szwergold, October 6, 2015

### Customzing the SNMP data a host passes.

To create a SNMP `snmpconf` file on the host, run this command as root:

	sudo snmpconf -i

Customize the config as you feel fit and then when it’s done, it will create a new `snmpd.conf` file here:

    /usr/share/snmp/snmpd.conf

Start the `snmpd` daemon like this:

    sudo snmpd

Stop the `snmpd` daemon like this:

    sudo killall snmpd

### Collection of SNMP data from a remote host.

Run this `snmpget` command to fetch data from a host; replace [hostname] with the name of the target host:

    /usr/bin/snmpget -c public [hostname]

Fetch the location info from the host:

    /usr/bin/snmpget -c public [hostname] system.sysLocation.0

Fetch the contact info from the host:

	/usr/bin/snmpget -c public [hostname] system.sysContact.0

Fetch the services info from the host:

	/usr/bin/snmpget -c public [hostname] system.sysServices.0

***

<sup>*Mac OS X - SNMP Basics (c) by Jack Szwergold. This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*</sup>