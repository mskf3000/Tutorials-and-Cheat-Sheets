## Linux-Unix - Time Related Items

By Jack Szwergold

### Setting time items on an Ubuntu/Debian system.

Force a manual system clock update with `ntpdate` usng the hostname `ntp.ubuntu.com`:

    sudo ntpdate ntp.ubuntu.com:

Set or change the system’s timezone:

    sudo dpkg-reconfigure tzdata

Create an `ntpdate` cron job file in `/etc/cron.daily/`:

    sudo nano /etc/cron.daily/ntpdate

Add this `ntpdate` command to that file; note the `-s` flag that will divert all standard output messages to the system log:

    ntpdate -s ntp.ubuntu.com

Make sure the `/etc/cron.daily/ntpdate` file has executable permissions:

    sudo chmod 755 /etc/cron.daily/ntpdate

### Setting time items on an CentOS/RedHat system.

Backup the current `localtime` timezone file by creating a simple side-by-side backup of it:

    sudo mv /etc/localtime /etc/localtime.bak

Now set a symbolic link between the timezone you want to use and `/etc/localtime`:

    sudo ln -s /usr/share/zoneinfo/America/New_York /etc/localtime

Install the `ntp` utilities to monitor system statistics via `yum`:

    sudo yum install ntp

Force a manual system clock update with `ntpdate` using the hostname `pool.ntp.org`:

    sudo ntpdate pool.ntp.org

Control NTP

    sudo /etc/init.d/ntpd start
    sudo /etc/init.d/ntpd stop

***

*Linux-Unix - Time Related Items (c) by Jack Szwergold; written on September 17, 2015. This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*
