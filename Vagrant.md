## Vagrant

By Jack Szwergold, January 23, 2016

### Basic usage.

This will init a basic Ubuntu 14.04 LTS (64-bit) setup:

    vagrant init ubuntu/trusty64

The response to this command will be:

	A `Vagrantfile` has been placed in this directory. You are now
	ready to `vagrant up` your first virtual environment! Please read
	the comments in the Vagrantfile as well as documentation on
	`vagrantup.com` for more information on using Vagrant.

A full list of available boxes can be found here:

    https://atlas.hashicorp.com/boxes/search

Once that Vagrant box has been downloaded, run this command to start the instance:

    vagrant up

This variant of the command will give you debugging info:

    vagrant up --debug

And this variant of the command will rerun the provisioning scripts on an already established Vagrant box:

    vagrant up --provision

SSH into the Vagrant box with this command:

    vagrant ssh

Stop the Vagrant box like this:

    vagrant halt

If you want to stop the Vagrant box and get rid of the installed disk/config from VirtualBox, run this command:

    vagrant destroy

You can change the default path that Vagrant uses to save remove boxes by entering this in the Terminal or adding it to your `.bash_profile`:

    export VAGRANT_HOME="/path/to/save/VagrantBoxes"

And the default SSH password for the user `vagrant` is `vagrant`.

Get the status of all Vagrant instances connected to your logged in user running this command:

    vagrant global-status

The output should be something like this:

	id       name                provider   state    directory
	----------------------------------------------------------------------------------------
	0b55b6e  default             virtualbox poweroff /Users/jack/Vagrant-Scripts/Ubuntu1404
	689d9e7  vagrant             virtualbox running  /Users/jack/Vagrant-Scripts
	28ee4e8  sandbox             virtualbox poweroff /Users/jack/Vagrant-Scripts
	b467de6  Sandbox_UBUNTU_1404 virtualbox poweroff /Users/jack/Vagrant-Scripts
	
	The above shows information about all known Vagrant environments
	on this machine. This data is cached and may not be completely
	up-to-date. To interact with any of the machines, you can go to
	that directory and run Vagrant, or you can use the ID directly
	with Vagrant commands from any directory. For example:
	"vagrant destroy 1a2b3c4d"

Note that list is a mess since a lot of the the machines listed don’t exist anymore for various reasons. So to clean up the list, run this command to prune the list:

    vagrant global-status --prune

And the list should be trimmed down as follows:

	id       name                provider   state    directory
	-------------------------------------------------------------------------------------
	b467de6  Sandbox_UBUNTU_1404 virtualbox poweroff /Users/jack/Vagrant-Scripts
	
	The above shows information about all known Vagrant environments
	on this machine. This data is cached and may not be completely
	up-to-date. To interact with any of the machines, you can go to
	that directory and run Vagrant, or you can use the ID directly
	with Vagrant commands from any directory. For example:
	"vagrant destroy 1a2b3c4d"

### Debugging Vagrant.

Check the SSH options for the Vagrant box:

    vagrant ssh-config

Output should be something like this:

	Host default
	  HostName 127.0.0.1
	  User vagrant
	  Port 2222
	  UserKnownHostsFile /dev/null
	  StrictHostKeyChecking no
	  PasswordAuthentication no
	  IdentityFile "/Users/jack/Vagrant_Ubuntu_1404/.vagrant/machines/default/virtualbox/private_key"
	  IdentitiesOnly yes
	  LogLevel FATAL

### Configuring Vagrant.

Okay, so with the basics done, you can edit the Vagrant config file (aka: `Vagrantfile`) to have a config like this:

	Vagrant.configure(2) do |config|
	  config.vm.box = "ubuntu/trusty64"
	  config.vm.box_check_update = false
	  config.vm.network "private_network", ip: "192.168.56.20"
	  config.vm.hostname = "vagrant"
	end

I removed all of the comments and just have a few of the core config options such as disabling the update checking and setting a private “host only” network to `192.168.56.20`.

***

### Creating your own Vagrant box.

Building a Vagrant box:

#### Build a basic OS box.

Set it up like any VirtualBox setup with the default user set as `sysop`. Just make sure the second Ethernet adapter is a “Host Only” adapter and gets the IP address of `192.168.56.20`. Temporarily bring up the interface like this:

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

With that done, let’s create a Vagrant package. Check your existing boxes like this:

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

*Vagrant (c) by Jack Szwergold. This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*