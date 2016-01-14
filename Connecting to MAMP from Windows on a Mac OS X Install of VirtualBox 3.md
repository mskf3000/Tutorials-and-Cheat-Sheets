# Connecting to MAMP from Windows on a Mac OS X Install of VirtualBox

By Jack Szwergold, March 2, 2014


You will basically be setting up a static IP network within the **192.165.65.x** subnet on the host-only adapter. So your virtual machine will have a standard **10.0.2.x** address on the main network interface and another IP address within the **192.165.65.x** subnet on the secondary host-only virtual interface.

1. Go to the *VirtualBox* menu.
2. Then choose *Preferences*.
3. Within *Preferences*, select *Network*.
4. And then choose *Host-only Networks*.

There should be at least one item in the pane called *vboxnet0*. If you do not see anything in that list—let alone *vboxnet0*—don’t worry. Just click the small icon that looks like a small PCI network interface card with a green “+” plus symbol to add one.

You only need to add two items to create a new adapter:

1. The IPv4 address of **192.168.56.1**.
2. The IPv4 network mask of **255.255.255.0**.

And then click the DHCP pane and make sure the DHCP server is disabled since this will be a static network.
 

Now to set it up on a `guest` machine just highlight whatever VirtualBox virtual machine you would like to to have this adapter and: 

1. Click *Settings*.
2. Then choose *Network*.

At this point you should see an *Adapter 1* pane and that should be set to NAT. Don’t touch that, but now click the *Adapter 2* pane and:

1. Check the *Enable Network Adapter* box.
2. And then for the *Attached to:* pull down select *Host-only Adapter*.
3. And then under *Name* select *vboxnet0*.
4. Once that’s all set, just click *OK* and you’re done.

### Conclusion.

Now when you launch your virtual machine, you can connect to the gateway address of the host-only adapter, which is **192.168.56.1**. So if you were connecting to MAMP via this setup, the address would be:

Again, for basic Windows browser compatibility testing, that might not be a factor but it is a nice option to have. And having a separate network interface for host-only traffic can help eliminate the potential issues associated with having all traffic going through the main **10.0.2.2** interface.

***

*Connecting to MAMP from Windows on a Mac OS X Install of VirtualBox • Part 3: Advanced — Using a Dual Network Interface Setup with a Host-Only Interface (c) by Jack Szwergold*

*This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*