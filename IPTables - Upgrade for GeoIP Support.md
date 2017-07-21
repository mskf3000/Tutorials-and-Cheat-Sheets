## IPTables - Upgrade for GeoIP Support

By Jack Szwergold

### For Reference Only! 100% none of this seems to work!

This is just here for refernece for now. Might not be worth the headache to get this setup for Ubuntu 12.04.5.

***

    sudo aptitude purge iptables iptables-persistent ubuntu-standard ufw

    sudo aptitude install iptables-persistent ubuntu-standard ufw

***

    curl -O -L http://mirrors.kernel.org/ubuntu/pool/main/i/iptables/libxtables10_1.4.21-1ubuntu1_amd64.deb
    sudo dpkg -i libxtables10_1.4.21-1ubuntu1_amd64.deb

    curl -O -L http://mirrors.kernel.org/ubuntu/pool/main/i/iptables/iptables_1.4.21-1ubuntu1_amd64.deb
    sudo dpkg -i iptables_1.4.21-1ubuntu1_amd64.deb

    curl -O -L http://mirrors.kernel.org/ubuntu/pool/main/i/iptables/iptables-dev_1.4.21-2ubuntu2_amd64.deb
    sudo dpkg -i iptables-dev_1.4.21-2ubuntu2_amd64.deb

***

    sudo aptitude purge ubuntu-standard ufw

***

Don’t run this command! But for reference, it would install `xtables-addons-common` version 1.41:

    sudo aptitude install xtables-addons-common

***

That is out of date and won’t work with IPTables `-m geoip`. The way to fix this is with `xtables-addons` version 2.5 or higher.

    curl -O -L http://old-releases.ubuntu.com/ubuntu/pool/universe/x/xtables-addons/xtables-addons-common_2.5-1_amd64.deb

Check the required dependencies like this:

    dpkg-deb -I xtables-addons-common_2.5-1_amd64.deb | grep Depends

The output is something like this:

    Pre-Depends: multiarch-support
    Depends: libc6 (>= 2.4), libxtables10

***

And then install it like this:

    sudo dpkg -i xtables-addons-common_2.5-1_amd64.deb

If you somehow need to uninstall this version `dpkg` install of `xtables-addons-common`, just do the following. First find out the package name like this:

    dpkg -l | grep xtables-addons-common

Knowing that just uninstall it like this:

    sudo dpkg -r xtables-addons-common:i386

***

    sudo aptitude install module-assistant

    sudo module-assistant prepare

    sudo aptitude install linux-headers-3.13.0-66-generic

    sudo module-assistant --verbose --text-mode auto-install xtables-addons

***

This downloads the GeoIP CSV files:

    sudo /usr/lib/xtables-addons/xt_geoip_dl

Install this tool to get import the CSV files into Xtables so it can be used by IPTables.

    sudo aptitude install libtext-csv-xs-perl

Create the GeoIP directory if it doesn’t already exist:

    sudo mkdir -p /usr/share/xt_geoip

Now run this command to build the GeoIP databases for IP ranges:

    sudo /usr/lib/xtables-addons/xt_geoip_build -D /usr/share/xt_geoip *.csv

The databases will be built in the following directories:

    ls -la /usr/share/xt_geoip/BE
    ls -la /usr/share/xt_geoip/LE

Check if the GeoIP module is installed:

    sudo cat /proc/net/ip_tables_matches

Check if the GeoIP module was installed:

    sudo iptables -m geoip --help

A test ban for (sadly) all of China:

    sudo iptables -A INPUT -m geoip --src-cc CN -j DROP

***

*IPTables - Upgrade for GeoIP Support (c) by Jack Szwergold; written on October 27, 2015. This work is licensed under a Creative Commons Attribution-NonCommercial 4.0 International License (CC-BY-NC-4.0).*
