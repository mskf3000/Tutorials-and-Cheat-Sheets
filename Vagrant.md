# Vagrant

By Jack Szwergold, January 23, 2016

***

## Create your own Vagrant box.

Building a Vagrant box:

#### Build a basic OS box.

Set it up like any VirtualBox setup with the default user set as `sysop`. Just make sure the second Ethernet adapter is a “Host Only” adapter and gets the IP address of `192.168.56.20`. Temporarilly bring up the interface like this:

    sudo ifconfig eth1 192.168.56.20 netmask 255.255.255.0 up
    
Open up the `/etc/network/interfaces` file:

	sudo nano /etc/network/interfaces

And add—or adjust—the interface details like this:

	# The local hostmachine access interface.
	auto eth1
	iface eth1 inet static
	address 192.168.56.20
	netmask 255.255.255.0

#### Start the VirtualBox virtual machine as a headless machine.

    nohup VBoxHeadless --startvm "Ubuntu 14.04" &

Slight variant of the command without a `nohup.out` file being created:

    nohup VBoxHeadless --startvm "Ubuntu 14.04" >/dev/null 2>&1 &

#### Setup a user named `vagrant`.

Use the password `vagrant`:

    sudo adduser vagrant

Add the `vagrant` user to the `sudo` group:

    sudo adduser vagrant sudo

#### Enable `sudo` without a password for the `vagrant` user.

	sudo mkdir -p /home/vagrant/.ssh/

Now create a `authorized_keys` file:

   sudo nano /home/vagrant/.ssh/authorized_keys

And add this Vagrant insecure public key to the authorized keys:

	ssh-rsa AAAAB3NzaC1yc2EAAAABIwAAAQEA6NF8iallvQVp22WDkTkyrtvp9eWW6A8YVr+kz4TjGYe7gHzIw+niNltGEFHzD8+v1I2YJ6oXevct1YeS0o9HZyN1Q9qgCgzUFtdOKLv6IedplqoPkcmF0aYet2PkEDo3MlTBckFXPITAMzF8dJSIFo9D8HfdOV0IAdx4O7PtixWKn5y2hMNG0zQPyUecp4pzC6kivAIhyfHilFR61RGL+GPXQ2MWZWFYbAGjyiYJnAmCP3NOTd0jMZEnDkbUvxhMmBYSdETk1rRgm+R4LOzFUGaHqHDLKLX+FIPKcF96hrucXzcWyLbIbEgE98OHlnVYCzRdK8jlqm8tehUc9c9WhQ== vagrant insecure public key

And now set the proper permissions for the `.ssh/` directory and the `authorized_keys` file:

    sudo chmod 700 /home/vagrant/.ssh
    sudo chmod 600 /home/vagrant/.ssh/authorized_keys

#### Creating the actual Vagrant box.

With that done, let’s createa Vagrant package. Check your existing boxes like this:

	vagrant box list

Output should be something like this:

	There are no installed boxes! Use `vagrant box add` to add some.

So to do that, let’s export the `Ubuntu 14.04` template we just setup like this:

	vagrant package --base 'Ubuntu 14.04' --output Ubuntu_14.04_template

The output should be something like this:

	==> Ubuntu 14.04: Exporting VM...
	==> Ubuntu 14.04: Compressing package to: /Users/jack/Ubuntu_14.04_template

Once that’s done, let’s add the exported template to the list of available Vagrant packages like this: 

    vagrant box add Ubuntu_14.04_template --name 'Ubuntu 14.04'

The output should be something like this:

	==> box: Box file was not detected as metadata. Adding it directly...
	==> box: Adding box 'Ubuntu 14.04' (v0) for provider: 
	    box: Unpacking necessary files from: file:///Users/jack/Ubuntu_14.04_template
	==> box: Successfully added box 'Ubuntu 14.04' (v0) for 'virtualbox'!

With that done, run the Vagrant box list command again like this:

    vagrant box list

And the output should be something like this:

	Ubuntu 14.04 (virtualbox, 0)

***

*Vagrant (c) by Jack Szwergold*

*This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*