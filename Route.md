## Route

By Jack Szwergold

### Setting a static route.

In this example we want a device to understand how to route all traffic on the `10.1.2.x` subnet

In Linux you would run a command like this:

    sudo route add -net 10.1.2.0/8 gw 192.168.56.10

Or a command like this:

    sudo route add -net 10.1.2.0 netmask 255.255.255.0 gw 192.168.56.10

And if you wanted this rule to persist after reboot, the you would need  to add this `post-up` line to `/etc/network/interfaces`:

    post-up route add -net 10.1.2.0 netmask 255.255.255.0 gw 192.168.56.10

To view the routing table you would run this `route` command:

    route -n

### An example of the same for Mac OS X.

In Mac OS X you would run a command like this:

    sudo route -n add -net 10.1.2.0/8 192.168.56.10

To view the routing table in Mac OS X you would run this Netstat command:

    netstat -nr

***

*Route (c) by Jack Szwergold; written October 22, 2015. This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*