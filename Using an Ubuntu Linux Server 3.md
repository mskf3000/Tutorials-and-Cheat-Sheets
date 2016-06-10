## Using an Ubuntu Linux Server

By Jack Szwergold, April 6, 2014

### Part 3: Monitoring and Securing Your Ubuntu Server

In part 3 of my tutorial I will explain how to setup useful monitoring and security tools. You should never be in a situation where you cannot be able to review and assess server health. Being able to monitor and secure your server is the key to running a safe and stable server environment.

#### Table of contents.

- [Install the ‘munin’ to monitor system vitals.](ubuntu_server_usage_part_3#install_munin)
- [Install ‘monit’ to monitor and manage specific services.](ubuntu_server_usage_part_3#install_monit)
- [Install the ‘iptables’ firewall.](ubuntu_server_usage_part_3#install_iptables)

#### <a name="install_munin"></a> Install the ‘munin’ to monitor system vitals.

The best way to keep running tabs on your server’s overall health is to use `munin`. It basically keeps a running, web accessible visual log of your server’s overall health. Thing of it as an EKG for your server. To use it, first install it like so:

    sudo aptitude install munin munin-node

If you notice we are installing `munin` itself as well as `munin-node` since `munin` can act as a centralized hub for the data `munin-node` generates. In this case we are going to host `munin` and `munin-node` on the same server.

Now, when `munin` is installed, it sets up a symbolic link from it’s default `apache` config to the real `apache` config area. I’m not so into that. So I like to remove that symbolic link:

    sudo rm /etc/apache2/conf.d/munin

Create a new `munin.conf` file like so:

    sudo nano /etc/apache2/conf.d/munin.conf

Then add this to that file:

    <Directory "/var/cache/munin/www">
      Options Indexes MultiViews FollowSymLinks
      AllowOverride None

      <IfModule mod_expires.c>
        ExpiresActive On
        ExpiresDefault M310
      </IfModule>

    </Directory>

Now wait about 10-15 minutes, restart `apache`:

    sudo service apache2 restart

And go to the default host or IP of your web server like so:

    http://192.168.56.10/munin

You should now see the beginnings of some Munin charts. Which is great! But let’s tune a few things first, such as activating some `apache`, `mysql` and `postfix` specific `munin` plugins like so:

    sudo ln -s /usr/share/munin/plugins/apache_accesses /etc/munin/plugins/apache_accesses
    sudo ln -s /usr/share/munin/plugins/apache_processes /etc/munin/plugins/apache_processes
    sudo ln -s /usr/share/munin/plugins/apache_volume /etc/munin/plugins/apache_volume
    sudo ln -s /usr/share/munin/plugins/mysql_bytes /etc/munin/plugins/mysql_bytes
    sudo ln -s /usr/share/munin/plugins/mysql_innodb /etc/munin/plugins/mysql_innodb
    sudo ln -s /usr/share/munin/plugins/mysql_queries /etc/munin/plugins/mysql_queries
    sudo ln -s /usr/share/munin/plugins/mysql_slowqueries /etc/munin/plugins/mysql_slowqueries
    sudo ln -s /usr/share/munin/plugins/mysql_threads /etc/munin/plugins/mysql_threads
    sudo ln -s /usr/share/munin/plugins/postfix_mailqueue /etc/munin/plugins/postfix_mailqueue
    sudo ln -s /usr/share/munin/plugins/postfix_mailvolume /etc/munin/plugins/postfix_mailvolume

And now restart `munin-node` for the changes to take effect:

    sudo service munin-node restart

Now there is an annoying bug in `munin` that will cause it to report InnoDB free tablespace to be low even when `autoextend` for MySQL is active. A complete false positive scenario that will drive you nuts if you have e-mail alerts set up. The solution is to add the following configuration options to your `munin-node` config like so:

    sudo nano /etc/munin/plugin-conf.d/munin-node

Now scroll to the bottom of the file and add these configuration settings:

    [mysql_innodb]
    env.warning 0
    env.critical 0

And now restart `munin-node` for the changes to take effect:

    sudo service munin-node restart

Also, for some reason the `iostat` plugin skips hard drive volumes that have a number in them by default. I’m not too sure what the benefit of doing that is, but can be annoying. To deactivate the default behavior, just open up the `munin-node` config file:

    sudo nano /etc/munin/plugin-conf.d/munin-node

And add these lines to the bottom of it like so:

    [iostat]
    env.SHOW_NUMBERED 1

And now restart `munin-node` for the changes to take effect:

    sudo service munin-node restart

Finally, if you want to receive e-mail alerts when `munin` notices something out of whack, open up the main `munin` config file like so:

    sudo nano /etc/munin/munin.conf

Now look for the lines that read:

    # Drop somejuser@fnord.comm and anotheruser@blibb.comm an email everytime
    # something changes (OK -> WARNING, CRITICAL -> OK, etc)
    #contact.someuser.command mail -s "Munin notification" somejuser@fnord.comm
    #contact.anotheruser.command mail -s "Munin notification" anotheruser@blibb.comm
    #
    # For those with Nagios, the following might come in handy. In addition,
    # the services must be defined in the Nagios server as well.
    #contact.nagios.command /usr/bin/send_nsca nagios.host.comm -c /etc/nsca.conf

And change that to add your e-mail contact info to the `contact.` settings. Be sure to change `my@emailaddress.com` to match your e-mail address:

    # Drop somejuser@fnord.comm and anotheruser@blibb.comm an email everytime
    # something changes (OK -> WARNING, CRITICAL -> OK, etc)
    #contact.someuser.command mail -s "Munin notification" somejuser@fnord.comm
    #contact.anotheruser.command mail -s "Munin notification" anotheruser@blibb.comm
    #

    contact.serveralert.command mail -s "MUNIN - ${var:group} :: ${var:host}" my@emailaddress.com
    contact.serveralert.always_send warning critical

    # For those with Nagios, the following might come in handy. In addition,
    # the services must be defined in the Nagios server as well.
    #contact.nagios.command /usr/bin/send_nsca nagios.host.comm -c /etc/nsca.conf

Now look for the lines that define the `host tree` settings like so:

    # a simple host tree
    [localhost]
        address 127.0.0.1
        use_node_name yes

And adjust them so it now has a `contacts` setting like so:

    # a simple host tree
    [localhost]
        address 127.0.0.1
        use_node_name yes
        contacts serveralert

And now restart `munin-node` for the changes to take effect:

    sudo service munin-node restart

Now `munin` should be fully setup and configured to not only monitor your server, but e-mail you with alerts when something odd comes up.

#### <a name="install_monit"></a> Install ‘monit’ to monitor and manage specific services.

A fantastic system administrator’s tool that I like to use—and have barely scratched the surface of—is `monit`. It’s basically a scriptable daemon that can monitor system services and take action on certain system service conditions based on your settings.

First, let’s install `monit` like so:

    sudo aptitude install monit

Now we want to adjust adjust `monit` so it has an e-mail server it can use. So let’s open the main `monit` config file:

    sudo nano /etc/monit/monitrc

Look for this chunk of commented out configuration code:

    ## Set the list of mail servers for alert delivery. Multiple servers may be
    ## specified using a comma separator. If the first mail server fails, Monit
    # will use the second mail server in the list and so on. By default Monit uses
    # port 25 - it is possible to override this with the PORT option.
    # 
    # set mailserver mail.bar.baz,               # primary mailserver
    #                backup.bar.baz port 10025,  # backup mailserver on port 10025
    #                localhost                   # fallback relay

Found it? Good! Now enter a space or two after that chunk and enter this configuration line for the mail server:

    set mailserver localhost

With that done, let’s set a simple `monit` script. The main thing I currently use `monit` for is to monitor `apache` and detect if the service is active. If it’s down? Then the script automatically restarts it. First, let’s create the `apache` config for `monit` like so:

    sudo nano /etc/monit/conf.d/apache2.conf

With that new file open, let’s copy this script into it. Be sure to change `my@emailaddress.com` to match your e-mail address:

    check process apache with pidfile /var/run/apache2.pid
            start "/etc/init.d/apache2 start"
            stop  "/etc/init.d/apache2 stop"
            if failed host 127.0.0.1 port 80
                    with timeout 15 seconds
            then restart
            alert my@emailaddress.com only on { timeout, nonexist }

Now restart `monit` like so:

    sudo service monit restart

That simple script will monitor your `apache` service on the localhost address of `127.0.0.1` running on port `80`. If it fails with a timeout after 15 seconds, it will automatically restart the `apache` service and alert you about the outage via e-mail. To me this is an invaluable tool to help keep a server up and running.

You can also add a server load average check to the mix like so:

    check process apache with pidfile /var/run/apache2.pid
            start "/etc/init.d/apache2 start"
            stop  "/etc/init.d/apache2 stop"
            if failed host 127.0.0.1 port 80
                    with timeout 15 seconds
            then restart
            if loadavg (1min) greater than 7
                    for 5 cycles
            then restart
            alert my@emailaddress.com only on { timeout, nonexist }

The configuration is just like the first one, but note the `if loadavg` section. That will check the system load average every minute. And if it has a load average that is greater than 7 for 5 one minute cycles, it will restart the `apache` service and alert you about the outage via e-mail. I selectively use the `loadavg` setting depending on server setup since many different factors can contribute to a high system load. But in general, a web server that is spiraling out of control due to DDoS can be saved by this script’s careful monitoring system load.

But always remember: While `monit` scripts can save you the headache of a server outage, but only a real human with the knowledge of how a server should work can determine what the real issue is. So if `monit` sends you an e-mail, you should take that alert seriously by logging into your server and monitoring it for a while.

#### <a name="install_iptables"></a> Install the ‘iptables’ firewall.

Now we’re going to install `iptables`, which is an excellent and widely used software-based firewall.  We’ll also be installing `iptables-persistent` which is a simply companion tool that allows `iptables` to be reloaded and active if/when a server reboots:

    sudo aptitude install iptables iptables-persistent ipset

Next, we’re going to create a very basic set of `iptables` rules to start off with. Below is a simple `iptables` rule set export of a basic setup I like to use:

	# Generated by iptables-save v1.4.12 on Sun Apr  6 11:50:24 2014
	*nat
	:PREROUTING ACCEPT [0:0]
	:INPUT ACCEPT [0:0]
	:OUTPUT ACCEPT [3:198]
	:POSTROUTING ACCEPT [3:198]
	COMMIT
	# Completed on Sun Apr  6 11:50:24 2014
	# Generated by iptables-save v1.4.12 on Sun Apr  6 11:50:24 2014
	*mangle
	:PREROUTING ACCEPT [22:1475]
	:INPUT ACCEPT [22:1475]
	:FORWARD ACCEPT [0:0]
	:OUTPUT ACCEPT [21:3342]
	:POSTROUTING ACCEPT [21:3342]
	COMMIT
	# Completed on Sun Apr  6 11:50:24 2014
	# Generated by iptables-save v1.4.12 on Sun Apr  6 11:50:24 2014
	*filter
	:INPUT ACCEPT [0:0]
	:FORWARD ACCEPT [0:0]
	:OUTPUT ACCEPT [21:3342]
	:SSH_CHECK - [0:0]
	:TOR - [0:0]
	
	# Accept specific IP addresses.
	-A INPUT -s 50.14.92.170/32 -p tcp -m tcp --dport 22 -j ACCEPT
	-A INPUT -p tcp -m tcp --dport 22 -m state --state NEW -j SSH_CHECK
	-A INPUT -j TOR
	-A INPUT -i lo -j ACCEPT
	
	# Drop invalid SYN packets.
	-A INPUT -p tcp --tcp-flags ALL ACK,RST,SYN,FIN -j REJECT
	-A INPUT -p tcp --tcp-flags SYN,FIN SYN,FIN -j REJECT
	-A INPUT -p tcp --tcp-flags SYN,RST SYN,RST -j REJECT
	
	# The combination of these TCP flags is not defined. Accepting packets so marked may cause unexpected results.
	-A INPUT -p tcp --tcp-flags FIN,SYN,RST,PSH,ACK,URG NONE -j REJECT
	-A INPUT -p tcp --tcp-flags FIN,SYN,RST,PSH,ACK,URG FIN,SYN,RST,PSH,ACK,URG -j REJECT
	-A INPUT -p tcp --tcp-flags FIN,SYN,RST,PSH,ACK,URG FIN,PSH,URG -j REJECT
	-A INPUT -p tcp --tcp-flags FIN,RST FIN,RST -j REJECT
	-A INPUT -p tcp --tcp-flags FIN,ACK FIN -j REJECT
	-A INPUT -p tcp --tcp-flags PSH,ACK PSH -j REJECT
	-A INPUT -p tcp --tcp-flags ACK,URG URG -j REJECT
	
	# Make sure new incoming tcp connections are SYN packets; otherwise we need to drop them. 
	-A INPUT -p tcp ! --syn -m state --state NEW -j REJECT
	
	# Drop packets with incoming fragments. This attack result into Linux server panic such data loss.
	-A INPUT -p tcp --tcp-flags ALL ALL -j REJECT
	
	# Accept ports.
	-A INPUT -p tcp -m tcp --dport 80 -j ACCEPT
	-A INPUT -p tcp -m tcp --dport 443 -j ACCEPT
	-A INPUT -p icmp -m icmp --icmp-type any -j ACCEPT
	-A INPUT -p esp -j ACCEPT
	-A INPUT -p ah -j ACCEPT
	-A INPUT -d 224.0.0.251/32 -p udp -m udp --dport 5353 -j ACCEPT
	-A INPUT -m state --state RELATED,ESTABLISHED -j ACCEPT
	-A INPUT -p tcp -m state --state NEW -m tcp --dport 22 -j ACCEPT
	-A INPUT -j REJECT --reject-with icmp-host-prohibited
	-A FORWARD -j TOR
	-A SSH_CHECK -m recent --set --name SSH --rsource
	-A SSH_CHECK -m recent --update --seconds 60 --hitcount 20 --name SSH --rsource -j DROP
	COMMIT
	# Completed on Sun Apr  6 11:50:24 2014

It seems like there is a lot of stuff happening here—and there is—but it’s pretty simple. Here’s a somewhat quick, but wordy explanation but if you don’t need these kind of details just yet, just move onto the next part: The stuff in `*nat` and `*mangle` are boilerplate defaults from an iptables rule set export. The stuff in `*filter` is where the basic cool stuff happens. The important lines in there includes the `SSH_CHECK` which protects against someone “war dialing” your SSH connection. It’s called as part of the `INPUT` chain at the top of the list, but it’s behavior is defined near the bottom of the list. The next set of rules define other sundry `INPUT` points, but the ones you should care about are the rules connected to port `80` as well as `443`. Those open up port `80` which is the basic `http` protocol port used by web servers and port `443` which is used by the `https` protocol used for secure webpage connections. If you need to open up any additional ports on your server, just copy the basic format shown in those two lines and change the port number to match the desired port number you need to have opened.

Now, run this command to immediately load the rules in `iptables.conf` into your server’s `iptables` setup:

    sudo iptables-restore < iptables.conf

Now if you run the following command:

    sudo iptables -L -n

You should see output that looks something like this which reflects all currently active `iptables` rules:

	Chain INPUT (policy ACCEPT)
	target     prot opt source               destination         
	ACCEPT     tcp  --  50.14.92.170         0.0.0.0/0            tcp dpt:22
	SSH_CHECK  tcp  --  0.0.0.0/0            0.0.0.0/0            tcp dpt:22 state NEW
	TOR        all  --  0.0.0.0/0            0.0.0.0/0           
	ACCEPT     all  --  0.0.0.0/0            0.0.0.0/0           
	REJECT     tcp  --  0.0.0.0/0            0.0.0.0/0            tcpflags: 0x3F/0x17 reject-with icmp-port-unreachable
	REJECT     tcp  --  0.0.0.0/0            0.0.0.0/0            tcpflags: 0x03/0x03 reject-with icmp-port-unreachable
	REJECT     tcp  --  0.0.0.0/0            0.0.0.0/0            tcpflags: 0x06/0x06 reject-with icmp-port-unreachable
	REJECT     tcp  --  0.0.0.0/0            0.0.0.0/0            tcpflags: 0x3F/0x00 reject-with icmp-port-unreachable
	REJECT     tcp  --  0.0.0.0/0            0.0.0.0/0            tcpflags: 0x3F/0x3F reject-with icmp-port-unreachable
	REJECT     tcp  --  0.0.0.0/0            0.0.0.0/0            tcpflags: 0x3F/0x29 reject-with icmp-port-unreachable
	REJECT     tcp  --  0.0.0.0/0            0.0.0.0/0            tcpflags: 0x05/0x05 reject-with icmp-port-unreachable
	REJECT     tcp  --  0.0.0.0/0            0.0.0.0/0            tcpflags: 0x11/0x01 reject-with icmp-port-unreachable
	REJECT     tcp  --  0.0.0.0/0            0.0.0.0/0            tcpflags: 0x18/0x08 reject-with icmp-port-unreachable
	REJECT     tcp  --  0.0.0.0/0            0.0.0.0/0            tcpflags: 0x30/0x20 reject-with icmp-port-unreachable
	REJECT     tcp  --  0.0.0.0/0            0.0.0.0/0            tcpflags:! 0x17/0x02 state NEW reject-with icmp-port-unreachable
	REJECT     tcp  --  0.0.0.0/0            0.0.0.0/0            tcpflags: 0x3F/0x3F reject-with icmp-port-unreachable
	ACCEPT     tcp  --  0.0.0.0/0            0.0.0.0/0            tcp dpt:80
	ACCEPT     tcp  --  0.0.0.0/0            0.0.0.0/0            tcp dpt:443
	ACCEPT     icmp --  0.0.0.0/0            0.0.0.0/0            icmptype 255
	ACCEPT     esp  --  0.0.0.0/0            0.0.0.0/0           
	ACCEPT     ah   --  0.0.0.0/0            0.0.0.0/0           
	ACCEPT     udp  --  0.0.0.0/0            224.0.0.251          udp dpt:5353
	ACCEPT     all  --  0.0.0.0/0            0.0.0.0/0            state RELATED,ESTABLISHED
	ACCEPT     tcp  --  0.0.0.0/0            0.0.0.0/0            state NEW tcp dpt:22
	REJECT     all  --  0.0.0.0/0            0.0.0.0/0            reject-with icmp-host-prohibited
	
	Chain FORWARD (policy ACCEPT)
	target     prot opt source               destination         
	TOR        all  --  0.0.0.0/0            0.0.0.0/0           
	
	Chain OUTPUT (policy ACCEPT)
	target     prot opt source               destination         
	
	Chain SSH_CHECK (1 references)
	target     prot opt source               destination         
	           all  --  0.0.0.0/0            0.0.0.0/0            recent: SET name: SSH side: source
	DROP       all  --  0.0.0.0/0            0.0.0.0/0            recent: UPDATE seconds: 60 hit_count: 20 name: SSH side: source
	
	Chain TOR (2 references)
	target     prot opt source               destination

Which is great! But the issue is that if you restart you server, all of your rules will be gone. That is where `iptables-persistent` comes in. And using it is pretty simple.

Just copy the `iptables.conf` file you created just now into `/etc/iptables/rules.v4`:

    sudo cp ~/iptables.conf /etc/iptables/rules.v4

Now when your server restarts, `iptables-persistent` will load all of the rules stored in `/etc/iptables/rules.v4` so your `iptables` rule set will always be active.

***

*Using an Ubuntu Linux Server • Part 3: Monitoring and Securing Your Ubuntu Server (c) by Jack Szwergold. This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*
