## Linux-Unix - Miscellaneous Items

By Jack Szwergold

### Get some basic system information like this.

Get the current Linux distribution name and related version information:

    cat /etc/*-release

And the output of that would be something like this:

	DISTRIB_ID=Ubuntu
	DISTRIB_RELEASE=12.04
	DISTRIB_CODENAME=precise
	DISTRIB_DESCRIPTION="Ubuntu 12.04.5 LTS"
	NAME="Ubuntu"
	VERSION="12.04.5 LTS, Precise Pangolin"
	ID=ubuntu
	ID_LIKE=debian
	PRETTY_NAME="Ubuntu precise (12.04.5 LTS)"
	VERSION_ID="12.04"

Another way of getting Linux distribution information:

    lsb_release -a

And the output of that would be something like this:

	No LSB modules are available.
	Distributor ID:	Ubuntu
	Description:	Ubuntu 12.04.5 LTS
	Release:	12.04
	Codename:	precise

Get the kernel release info:

    uname -r

And the output of that would be something like this:

    3.13.0-65-generic

Get the machine name, kernel release info and kernel name:

    uname -mrs

And the output of that would be something like this:

    Linux 3.13.0-65-generic x86_64

Get all system information:

    uname -a

And the output of that would be something like this:

    Linux sandbox 3.13.0-65-generic #105~precise1-Ubuntu SMP Tue Sep 22 13:22:42 UTC 2015 x86_64 x86_64 x86_64 GNU/Linux

Get the the number of processing units—CPUs or cores—available:

    nproc

Check if the OS is 32-bit or 64-bit by running this command:

    file /sbin/init

And the output of that would be something like this:

	/sbin/init: ELF 64-bit LSB shared object, x86-64, version 1 (SYSV), dynamically linked (uses shared libs), for GNU/Linux 2.6.24, BuildID[sha1]=0x2b5d2db96270e85fa5e68475e37a5e94c479a396, stripped

### Locale specific items.

Fix a “setting locale failed” warning by running this command:

    sudo locale-gen en_US en_US.UTF-8

And then run this to regenerate local related items:

    sudo dpkg-reconfigure locales

Check the output of `locale` which should now be something like this:

	LANG=en_US.UTF-8
	LANGUAGE=
	LC_CTYPE="en_US.UTF-8"
	LC_NUMERIC="en_US.UTF-8"
	LC_TIME="en_US.UTF-8"
	LC_COLLATE="en_US.UTF-8"
	LC_MONETARY="en_US.UTF-8"
	LC_MESSAGES="en_US.UTF-8"
	LC_PAPER="en_US.UTF-8"
	LC_NAME="en_US.UTF-8"
	LC_ADDRESS="en_US.UTF-8"
	LC_TELEPHONE="en_US.UTF-8"
	LC_MEASUREMENT="en_US.UTF-8"
	LC_IDENTIFICATION="en_US.UTF-8"
	LC_ALL=

### Set the default test editor in Ubuntu/Debian.

    sudo update-alternatives --config editor

### Adjust DNS resolution settings.

Note that on some setups, this file exists but shouldn’t be edited because it’s overwritten but some other DNS setup.

    nano /etc/resolv.conf

If somehow the `resolv.conf` file gets mucked up, try regenerating it like this:

    sudo resolvconf -u

And if somehow that command doesn’t work, run this `dpkg-reconfigure` command:

    sudo dpkg-reconfigure resolvconf

### How to deal with Linux software package archives.

First extract the items from an Ubuntu/Debian `.deb` archive:

    tar vx mypackage.deb

Once that is done then un-Tar/un-GZip the resulting archive:

    tar -xzvf data.tar.gz

Extracting items from a CentOS/RedHat `.rpm` archive:

    rpm2cpio mypackage.rpm | cpio -vid

### Location of the CentOS/RedHat ProtectBase configuration file.

    sudo nano /etc/yum/pluginconf.d/protectbase.conf

### Simple command line process management with `nohup` and `&`.

Send a command to the background and allow logout.

    nohup [command] &

### Using `wc` to get word counts and line counts.

Get the line count of all files with a `*.txt` extension:

    wc -l *.txt

Get the word count of all files with a `*.txt` extension:

    wc -w *.txt

### Check system memory usage.

Get the amount of free memory in kilobytes:

    free -k

Get the amount of free memory in megabytes:

    free -m

### Check and manage processes.

Find active processes

    ps -ax

Find active processes with grep

    ps -ax | grep pts

Kill all processes that match the name of the process:

    pkill -9 [name of process]

### Using `df` to disk free space and inode usage.

Display hard disk partition size in kilobytes:

    df -k

Display hard disk partition size in human readable megabytes:

    df -h

Display inode size in kilobytes:

    df -i

Display inode size in human readable megabytes:

    df -ih

### Using `du` to display disk usage.

Display disk usage in human readable megabytes with a maximum depth of 1:

    sudo du -h -d=1

Display disk usage for all items in the immediate directory (`s` = `d=1`) with block counts in human readable megabytes:

    du -skh *

### Using `lsof`.

Display the list of open files with `lsof`:

    lsof

Use `losf` to see what ports are connected to a process:

    lsof -i -n -P | grep sendmail

***

*Linux-Unix - Miscellaneous Items (c) by Jack Szwergold; written October 4, 2015. This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*