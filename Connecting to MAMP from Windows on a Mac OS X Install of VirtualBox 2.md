## Connecting to MAMP from Windows on a Mac OS X Install of VirtualBox

By Jack Szwergold

### Part 2: Basic — Using a Single Network Interface Setup

In this tutorial I am going to outline one of the most basic ways to connect to a `host` machine from a `guest` machine within VirtualBox from a fresh Windows `guest` OS install using the main VirtualBox network interface.

This is being done primarily for Microsoft Internet Explorer web browser cross-platform compatibility testing with MAMP-based development tools in Mac OS X. But the basic concepts of this kind of setup can be used for all sorts of things beyond testing web browser cross-platform browser compatibly.

The main drawback of this method is there is no easy way to connect from the `host` machine to the `guest`, but that is not a big issue for basic Internet Explorer testing. If you think you might need that extra level of two-way connectivity, skip to the second part of this tutorial where I explain how you can use dual network interfaces with a special host-only adapter in VirtualBox.

#### Let’s get started.

The most immediate way you can connect to a `host` machine is to use the default VirtualBox user network gateway of **10.0.2.2**.

So if you were to connect to MAMP on Mac OS X running on port **8888**, the address to connect to would be:

	http://10.0.2.2:8888

But that is far from ideal since the MAMP setup I like to use utilizes Apache name-based virtual hosts to allow for there to be one root at:

	http://localhost:8888

And then another one based on your machine ID of—for example—**MY_MACHINE** like this:

	http://MY_MACHINE.local:8888

#### The problem with this and how to solve it.

The problem? Two things.

1. **Localhost is Relative:** One, since **localhost** is universally considered a machine’s own loopback address, the actual **localhost** on a `guest` OS in VirtualBox would be the loopback of the `guest` OS itself. When in reality you want the **localhost** of the `host` machine. Which is an entirely different machine from a virtualization standpoint.
2. **Multicast DNS Broken in VirtualBox:** The other issue is that due to the fact that Windows and VirtualBox do not play well together with regards to multicast network packets—let alone MDNS (multicast DNS)—a machine hostname like **MY_MACHINE.local:8888** will never be visible to Windows in VirtualBox.

The solution to both of these issues for our purposes is to simply edit the `hosts` file on the Windows `guest` OS. The file is located here.

	c:\windows\system32\drivers\etc\hosts

And should be edited via a text editor—such as Notepad—run as an administrator.

Since the `hosts` file user editable when running Windows in VirtualBox, so you can create hostname aliases of your choice. But for this example I would use **MY_MACHINE** like so.

	10.0.2.2	MY_MACHINE
	10.0.2.2	MY_MACHINE.local

Doing this, you would be able to connect to the equivalent of the following.

* **MY_MACHINE:8888** would be the same as **localhost:8888**
* **MY_MACHINE.local:8888** would be the same as **MY_MACHINE.local:8888**

#### Conclusion.

So now you should be able to connect to anything on **MY_MACHINE.local:8888** and view it as if it were being viewed from MAMP on your Mac desktop. But in the case of the localhost equivalent of **MY_MACHINE:8888** depending on the codebase configuration of whatever you are testing, you might need to temporarily adjust the config files in your local MAMP set to reflect the new **MY_MACHINE:8888** address since **localhost:8888** won’t be reachable.

***

*Connecting to MAMP from Windows on a Mac OS X Install of VirtualBox • Part 2: Basic — Using a Single Network Interface Setup (c) by Jack Szwergold; written on March 2, 2014. This work is licensed under a Creative Commons Attribution-NonCommercial 4.0 International License (CC-BY-NC-4.0).*
