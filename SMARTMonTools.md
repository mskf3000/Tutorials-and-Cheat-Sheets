## SMARTMonTools

By Jack Szwergold, September 23, 2015

Then install SMARTMonTools via `aptitude` like this:

    sudo aptitude install smartmontools

### A simple check of SMART status from the command line.

Check the SMART status of `/dev/sda`:

    sudo smartctl -i /dev/sda

Output would be something like this; note this device doesn’t have SMART capability but the report format is similar for SMART capable devices:

	smartctl 5.41 2011-06-09 r3365 [x86_64-linux-3.13.0-63-generic] (local build)
	Copyright (C) 2002-11 by Bruce Allen, http://smartmontools.sourceforge.net
	
	=== START OF INFORMATION SECTION ===
	Device Model:     VBOX HARDDISK
	Serial Number:    VBbe53cb18-051acb9e
	Firmware Version: 1.0
	User Capacity:    34,359,738,368 bytes [34.3 GB]
	Sector Size:      512 bytes logical/physical
	Device is:        Not in smartctl database [for details use: -P showall]
	ATA Version is:   6
	ATA Standard is:  ATA/ATAPI-6 published, ANSI INCITS 361-2002
	Local Time is:    Thu Sep 24 00:21:13 2015 EDT
	SMART support is: Unavailable - device lacks SMART capability.

### Enable the SMARTMonTools daemon (`smartd`) on startup.

First open up the `smartmontools` daemon config file:

    sudo nano /etc/default/smartmontools

And uncomment the line that reads `start_smartd=yes`:

	# uncomment to start smartd on system startup
	start_smartd=yes

### Adding a device to the `smartd.conf` file for monitoring.

First open up the `smartmontools` config file:

	sudo nano /etc/smartd.conf
	
And add some monitoring configs like this to the bottom; note only one is active and the other is commented out as an example:

	# 2015-09-24: Example config.
	#/dev/sda -a -d sat -I 194 -m email_address@example.com -M test -s (S/../.././02|L/../../6/03)
	# /dev/sda -a -d ata -I 194 -m email_address@example.com -M test -s (S/../.././02|L/../../6/03)
	/dev/sda1 -a -d ata -I 194 -m email_address@example.com -M test -s (S/../.././02|L/../../6/03)
	
### Adding to the `smartd.conf` file to Munin.

Create a symbolic link for the plugin to be active:

    sudo ln -s /usr/share/munin/plugins/smart_ /etc/munin/plugins/smart_hda

Check the config:

    sudo nano /etc/munin/plugin-conf.d/munin-node

Check to see if `[smart_*]` entry exists. If it doesn’t just add this simple config to bottom of the file:

	[smart_*]
	user root

### Sample contents from the `smartd.conf` for reference.

	# Sample configuration file for smartd.  See man smartd.conf.
	
	# Home page is: http://smartmontools.sourceforge.net
	
	# smartd will re-read the configuration file if it receives a HUP
	# signal
	
	# The file gives a list of devices to monitor using smartd, with one
	# device per line. Text after a hash (#) is ignored, and you may use
	# spaces and tabs for white space. You may use '\' to continue lines.
	
	# You can usually identify which hard disks are on your system by
	# looking in /proc/ide and in /proc/scsi.
	
	# The word DEVICESCAN will cause any remaining lines in this
	# configuration file to be ignored: it tells smartd to scan for all
	# ATA and SCSI devices.  DEVICESCAN may be followed by any of the
	# Directives listed below, which will be applied to all devices that
	# are found.  Most users should comment out DEVICESCAN and explicitly
	# list the devices that they wish to monitor.
	#DEVICESCAN -d removable -n standby -m root -M exec /usr/share/smartmontools/smartd-runner
	
	# Alternative setting to ignore temperature and power-on hours reports
	# in syslog.
	#DEVICESCAN -I 194 -I 231 -I 9
	
	# Alternative setting to report more useful raw temperature in syslog.
	#DEVICESCAN -R 194 -R 231 -I 9
	
	# Alternative setting to report raw temperature changes >= 5 Celsius
	# and min/max temperatures.
	#DEVICESCAN -I 194 -I 231 -I 9 -W 5
	
	# First (primary) ATA/IDE hard disk.  Monitor all attributes, enable
	# automatic online data collection, automatic Attribute autosave, and
	# start a short self-test every day between 2-3am, and a long self test
	# Saturdays between 3-4am.
	#/dev/hda -a -o on -S on -s (S/../.././02|L/../../6/03)
	
	# Monitor SMART status, ATA Error Log, Self-test log, and track
	# changes in all attributes except for attribute 194
	#/dev/hdb -H -l error -l selftest -t -I 194
	
	# Monitor all attributes except normalized Temperature (usually 194),
	# but track Temperature changes >= 4 Celsius, report Temperatures
	# >= 45 Celsius and changes in Raw value of Reallocated_Sector_Ct (5).
	# Send mail on SMART failures or when Temperature is >= 55 Celsius.
	#/dev/hdc -a -I 194 -W 4,45,55 -R 5 -m admin@example.com
	
	# An ATA disk may appear as a SCSI device to the OS. If a SCSI to
	# ATA Translation (SAT) layer is between the OS and the device then
	# this can be flagged with the '-d sat' option. This situation may
	# become common with SATA disks in SAS and FC environments.
	# /dev/sda -a -d sat
	
	# A very silent check.  Only report SMART health status if it fails
	# But send an email in this case
	#/dev/hdc -H -C 0 -U 0 -m admin@example.com
	
	# First two SCSI disks.  This will monitor everything that smartd can
	# monitor.  Start extended self-tests Wednesdays between 6-7pm and
	# Sundays between 1-2 am
	#/dev/sda -d scsi -s L/../../3/18
	#/dev/sdb -d scsi -s L/../../7/01
	
	# Monitor 4 ATA disks connected to a 3ware 6/7/8000 controller which uses
	# the 3w-xxxx driver. Start long self-tests Sundays between 1-2, 2-3, 3-4,
	# and 4-5 am.
	# NOTE: starting with the Linux 2.6 kernel series, the /dev/sdX interface
	# is DEPRECATED.  Use the /dev/tweN character device interface instead.
	# For example /dev/twe0, /dev/twe1, and so on.
	#/dev/sdc -d 3ware,0 -a -s L/../../7/01
	#/dev/sdc -d 3ware,1 -a -s L/../../7/02
	#/dev/sdc -d 3ware,2 -a -s L/../../7/03
	#/dev/sdc -d 3ware,3 -a -s L/../../7/04
	
	# Monitor 2 ATA disks connected to a 3ware 9000 controller which
	# uses the 3w-9xxx driver (Linux, FreeBSD). Start long self-tests Tuesdays
	# between 1-2 and 3-4 am.
	#/dev/twa0 -d 3ware,0 -a -s L/../../2/01
	#/dev/twa0 -d 3ware,1 -a -s L/../../2/03
	
	# Monitor 2 SATA (not SAS) disks connected to a 3ware 9000 controller which
	# uses the 3w-sas driver (Linux, FreeBSD). Start long self-tests Tuesdays
	# between 1-2 and 3-4 am.
	#/dev/twl0 -d 3ware,0 -a -s L/../../2/01
	#/dev/twa0 -d 3ware,1 -a -s L/../../2/03
	
	# Same as above for Windows. Option '-d 3ware,N' is not necessary,
	# disk (port) number is specified in device name.
	# NOTE: On Windows, DEVICESCAN works also for 3ware controllers.
	#/dev/hdc,0 -a -s L/../../2/01
	#/dev/hdc,1 -a -s L/../../2/03
	#
	# Monitor 2 disks connected to the first HP SmartArray controller which
	# uses the cciss driver. Start long tests on Sunday nights and short
	# self-tests every night and send errors to root
	#/dev/cciss/c0d0 -d cciss,0 -a -s (L/../../7/02|S/../.././02) -m root
	#/dev/cciss/c0d0 -d cciss,1 -a -s (L/../../7/03|S/../.././03) -m root
	
	# Monitor 3 ATA disks directly connected to a HighPoint RocketRAID. Start long
	# self-tests Sundays between 1-2, 2-3, and 3-4 am.
	#/dev/sdd -d hpt,1/1 -a -s L/../../7/01
	#/dev/sdd -d hpt,1/2 -a -s L/../../7/02
	#/dev/sdd -d hpt,1/3 -a -s L/../../7/03
	
	# Monitor 2 ATA disks connected to the same PMPort which connected to the
	# HighPoint RocketRAID. Start long self-tests Tuesdays between 1-2 and 3-4 am
	#/dev/sdd -d hpt,1/4/1 -a -s L/../../2/01
	#/dev/sdd -d hpt,1/4/2 -a -s L/../../2/03
	
	# HERE IS A LIST OF DIRECTIVES FOR THIS CONFIGURATION FILE.
	# PLEASE SEE THE smartd.conf MAN PAGE FOR DETAILS
	#
	#   -d TYPE Set the device type: ata, scsi, marvell, removable, 3ware,N, hpt,L/M/N
	#   -T TYPE set the tolerance to one of: normal, permissive
	#   -o VAL  Enable/disable automatic offline tests (on/off)
	#   -S VAL  Enable/disable attribute autosave (on/off)
	#   -n MODE No check. MODE is one of: never, sleep, standby, idle
	#   -H      Monitor SMART Health Status, report if failed
	#   -l TYPE Monitor SMART log.  Type is one of: error, selftest
	#   -f      Monitor for failure of any 'Usage' Attributes
	#   -m ADD  Send warning email to ADD for -H, -l error, -l selftest, and -f
	#   -M TYPE Modify email warning behavior (see man page)
	#   -s REGE Start self-test when type/date matches regular expression (see man page)
	#   -p      Report changes in 'Prefailure' Normalized Attributes
	#   -u      Report changes in 'Usage' Normalized Attributes
	#   -t      Equivalent to -p and -u Directives
	#   -r ID   Also report Raw values of Attribute ID with -p, -u or -t
	#   -R ID   Track changes in Attribute ID Raw value with -p, -u or -t
	#   -i ID   Ignore Attribute ID for -f Directive
	#   -I ID   Ignore Attribute ID for -p, -u or -t Directive
	#   -C ID   Report if Current Pending Sector count non-zero
	#   -U ID   Report if Offline Uncorrectable count non-zero
	#   -W D,I,C Monitor Temperature D)ifference, I)nformal limit, C)ritical limit
	#   -v N,ST Modifies labeling of Attribute N (see man page)
	#   -a      Default: equivalent to -H -f -t -l error -l selftest -C 197 -U 198
	#   -F TYPE Use firmware bug workaround. Type is one of: none, samsung
	#   -P TYPE Drive-specific presets: use, ignore, show, showall
	#    #      Comment: text after a hash sign is ignored
	#    \      Line continuation character
	# Attribute ID is a decimal integer 1 <= ID <= 255
	# except for -C and -U, where ID = 0 turns them off.
	# All but -d, -m and -M Directives are only implemented for ATA devices
	#
	# If the test string DEVICESCAN is the first uncommented text
	# then smartd will scan for devices /dev/hd[a-l] and /dev/sd[a-z]
	# DEVICESCAN may be followed by any desired Directives.

***

*SMARTMonTools (c) by Jack Szwergold. This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*
