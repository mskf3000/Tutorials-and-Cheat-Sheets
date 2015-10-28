# Cheat Sheet - IPTables - IPSet

By Jack Szwergold, October 27, 2015

#### Installing IPSet.

Install `ipset`:

    sudo aptitude install ipset

#### Some of the basics.

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

#### How to block a whole country’s IP range—in this case China—with IPSet.

Preface to all of this is: I *truly and utterly hate* the idea of blocking a whole countries range of IP addresses, but the case of Chinese traffic to a few of the servers I manage, it makes sense. These are web servers that cater to a western audience and have no appeal outside of the U.S. let alone China.

And to the meat of the matter, the amount of malicious robot/hacking attempt traffic coming from China nowadays is pretty insane. Past any of that, most people who are not robots/hackers in China who pursue Western content use VPNs that make their access endpoint another nation entirely. Meaning that legitimate web users in China will not be blocked by this technique.

That said, still I hate it. But until the robot/hacking attempt traffic from China gets better managed or throttled by Chinese ISPs this is the lesser of all current evils to protect a web server from unwanted robot/hacking attempts.

***

First, let’s create a China specific (`CN`) IP set like this:

    sudo ipset create CN_range hash:net

That will just create the `ipset.CN_range.conf` file itself with the first line being `create FooBar hash:net`. We will populate it with entries in the next steps.

Now let’s download the raw `cn.zone` file from the IP Deny website like this:

	curl -O -L http://www.ipdeny.com/ipblocks/data/countries/cn.zone

With that downloaded, lets now populate the `CN_range` IP set config file with the values from the `cn.zone` file like this:

	awk '{print "add CN_range " $0}' cn.zone > ipset.CN_range.conf

With that done, let’s import the `CN_range` IP set like this:

    sudo ipset restore < ipset.CN_range.conf

Now check the entries in the IP set by running this command:

    sudo ipset -l CN_range | more

If that all looks good, let’s tell IPTables to pay attention to that `CN_range` set like this:

	sudo iptables -A INPUT -p tcp -m set --match-set CN_range src -j REJECT

And if somehow you needed to remove that IPTables rule, run this command:

    sudo iptables -D INPUT -p tcp -m set --match-set CN_range src -j REJECT

***

*Cheat Sheet - IPTables - IPSet (c) by Jack Szwergold*

*This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*
