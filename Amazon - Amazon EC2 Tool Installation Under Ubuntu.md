## Amazon - Amazon EC2 Tool Installation Under Ubuntu

By Jack Szwergold

Edit the sources list:

	sudo nano /etc/apt/sources.list

Uncomment the lines for `multiverse` repos:

	deb http://us-west-2.ec2.archive.ubuntu.com/ubuntu/ trusty multiverse
	deb-src http://us-west-2.ec2.archive.ubuntu.com/ubuntu/ trusty multiverse
	deb http://us-west-2.ec2.archive.ubuntu.com/ubuntu/ trusty-updates multiverse
	deb-src http://us-west-2.ec2.archive.ubuntu.com/ubuntu/ trusty-updates multiverse

Then update the repositories:

	sudo aptitude update

Now you can install the Amazon EC2 API tools:

    sudo aptitude install ec2-api-tools

This is an example command that allows one to open the Amazone EC2 security set to all ICMP traffic instead of just SSH traffic on port `22`:

    ec2-authorize -O "$AWS_ACCES_KEY" -W "$AWS_SECRET_KEY" default -P icmp -t -1:-1 -s 0.0.0.0/0

***

*Amazon - Amazon EC2 Tool Installation Under Ubuntu (c) by Jack Szwergold; written on January 17, 2016. This work is licensed under a Creative Commons Attribution-NonCommercial 4.0 International License (CC-BY-NC-4.0).*