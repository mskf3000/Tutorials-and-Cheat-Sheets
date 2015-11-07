# IPTables - IPSet

By Jack Szwergold, October 27, 2015

***

### Installing IPSet.

Install `ipset`:

    sudo aptitude install ipset

### Some of the basics.

Create an IP set like this:

    sudo ipset -N FooBar hash:net

Check that the IP set exists by listing the all sets like this:

	sudo ipset -l

The output at this point would be something like this:

	Name: FooBar
	Type: hash:net
	Header: family inet hashsize 1024 maxelem 65536
	Size in memory: 16760
	References: 0
	Members:

Now let’s add a few IPs to that set like this:

    sudo ipset add FooBar 10.0.0.0/8
    sudo ipset add FooBar 127.0.0.0/8
    sudo ipset add FooBar 192.168.0.0/16

And if you run IP set again:

	sudo ipset -l

The additions will be reflected in the new output:

	Name: FooBar
	Type: hash:net
	Header: family inet hashsize 1024 maxelem 65536
	Size in memory: 16856
	References: 0
	Members:
	127.0.0.0/8
	192.168.0.0/16
	10.0.0.0/8

And can then save the IP set like this

    sudo ipset save FooBar > ipset.FooBar.conf

The contents of `ipset.FooBar.conf` it will look something like this:

	create FooBar hash:net family inet hashsize 1024 maxelem 65536
	add FooBar 127.0.0.0/8
	add FooBar 10.0.0.0/8
	add FooBar 192.168.0.0/16

At this point if you wanted to destroy the `FooBar` IP set, you can do so by doing this:

    sudo ipset destroy FooBar

To remove all entries from the `FooBar` IP set, just “flush” it like this:

    sudo ipset flush FooBar

And you can restore the IP set like this:

    sudo ipset restore < ipset.FooBar.conf

### Get IPSet to retain values on reboot.

If you are using IPSet you are using IPTables as well. And if you are using IPTables, you are using `iptables-persistent` to ensure the IPTables rules are restored on reboot, right? But what about something for IPSet like `ipset-persistent`? Does that exist?

Sadly, there is no equivalent of `iptables-persistent` out there. But you can tweak the core `iptables-persistent` script to get it to load IP set rules before the IPTables stuff gets loaded.

First, copy the IP set rules into a text file like this:

    sudo ipset save > ~/ipset.conf

Then copy those rules into a file named `rules.ipsets` in the `/etc/iptables/` directory like this:

	sudo cp ~/ipset.conf /etc/iptables/rules.ipsets

Now let’s tweak the `iptables-persistent` startup script like this:

    sudo nano /etc/init.d/iptables-persistent

Add this to `load_rules()` right after `log_action_begin_msg "Loading iptables rules"`:

    #load IPsets
    if [ ! -f /etc/iptables/rules.ipsets ]; then
      log_action_cont_msg " skipping IPsets (no rules to load)"
    else
      log_action_cont_msg " IPset"
      ipset restore -! < /etc/iptables/rules.ipsets 2> /dev/null
      if [ $? -ne 0 ]; then
        rc=1
      fi
    fi

Add this to `save_rules()` right after `log_action_begin_msg "Saving rules"`:

	#save IPsets
	#need at least iptable_filter loaded:
	if ! ipset list | grep -i "name">/dev/null 2>&1; then
	  log_action_cont_msg " skipping IPset - no sets defined or not loaded"
	elif [ -x /usr/sbin/ipset ] || [ -x /sbin/ipset ]; then
	  log_action_cont_msg " IPset"
	  ipset save | grep -iv "f2b"> /etc/iptables/rules.ipsets
	  if [ $? -ne 0 ]; then
	    rc=1
	  fi
	fi

That said this is a clean/messy solution. If anything better comes along, gotta ditch this.

### How to block a whole country’s IP range with IPSet.

Preface to all of this is: I *truly and utterly hate* the idea of blocking a whole countries range of IP addresses, but in the case of  network traffic to a few of the servers I manage, it makes sense since they are being slammed by traffic from China. These are web servers that cater to a western audience and have no appeal outside of the U.S. let alone China.

And to the meat of the matter, the amount of malicious robot/hacking attempt traffic coming from China nowadays is pretty insane. Past any of that, most people who are not robots/hackers in China who pursue Western content use VPNs that make their access endpoint another nation entirely. Meaning that legitimate web users in China will not be blocked by this technique.

That said, still I hate it. But until the robot/hacking attempt traffic from China gets better managed or throttled by ISPs in China this is the lesser of all current evils to protect a web server from unwanted robot/hacking attempts.

***

#### Using a slash notated zone file from a website.

First, let’s create a generic `BANNED_RANGES` IP set like this:

    sudo ipset create BANNED_RANGES hash:net

Now let’s download a raw zone file—such as `cn.zone`— from the IP Deny website like this:

	curl -O -L http://www.ipdeny.com/ipblocks/data/countries/cn.zone

With that downloaded, lets now populate the `BANNED_RANGES` IP set config file with the values from the `cn.zone` file like this:

	awk '{print "add BANNED_RANGES " $0}' cn.zone > ipset.BANNED_RANGES.conf

With that done, let’s import the `BANNED_RANGES` IP set like this:

    sudo ipset restore < ipset.BANNED_RANGES.conf

Now check the entries in the IP set by running this command:

    sudo ipset -l BANNED_RANGES | more

#### Using the GeoIP country CSV databse.

This method is the preferred method since `GeoIPCountryCSV.zip` contains all countries and not just the data from one URL file. Also, since the `GeoIPCountryCSV.zip` can be stored locally, it assures us that even if a remote server serving zone files goes offline we still have some data source to work with.

***

First, let’s create a generic `BANNED_RANGES` IP set like this:

    sudo ipset create BANNED_RANGES hash:net

Now let’s download the `GeoIPCountryCSV.zip` from the official MaxMind GeoIP website:

	curl -O -L http://geolite.maxmind.com/download/geoip/database/GeoIPCountryCSV.zip

Let’s decompress it like this:

	unzip -o -q -d . GeoIPCountryCSV.zip

And with the archive decompressed, let’s ditch the remaning `GeoIPCountryCSV.zip`:

	rm GeoIPCountryCSV.zip
	
With that done, lets now populate the `BANNED_RANGES` IP set config file with the values from the `GeoIPCountryWhois.csv` file like this; note we are focusing only on China (`CN`) IP addresses:

	awk -F "," -v COUNTRY_CODE=CN -v IPSET_TABLE=BANNED_RANGES} '$5 ~ COUNTRY_CODE { gsub(/"/, "", $1); gsub(/"/, "", $2); print "add "IPSET_TABLE" "$1"-"$2; }' /usr/local/share/GeoIP/GeoIPCountryWhois.csv >> ipset.BANNED_RANGES.conf
	
With that done, let’s import the `BANNED_RANGES` IP set like this:

    sudo ipset restore < ipset.BANNED_RANGES.conf

Now check the entries in the IP set by running this command:

    sudo ipset -l BANNED_RANGES | more

#### Making IPTables aware of the `BANNED_RANGES` IP Set.

If that all looks good, let’s tell IPTables to pay attention to that `BANNED_RANGES` set like this:

	sudo iptables -I INPUT -p tcp -m set --match-set BANNED_RANGES src -j REJECT

To remove all entries from the `BANNED_RANGES` IP set, just “flush” it like this:

    sudo ipset flush BANNED_RANGES

And if somehow you need to remove that IPTables rule, run this command:

    sudo iptables -D INPUT -p tcp -m set --match-set BANNED_RANGES src -j REJECT

***

*IPTables - IPSet (c) by Jack Szwergold*

*This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*
