# Ruby - Installing Ruby via RVM

By Jack Szwergold, September 20, 2015

#### Installing RVM (Ruby Version Manager).
	
	gpg --keyserver hkp://keys.gnupg.net --recv-keys D39DC0E3
	
	\curl -L https://get.rvm.io | bash -s stable
	
	source ~/.rvm/scripts/rvm
	
	rvm requirements

Install the basics for the RVM build:
	
	sudo aptitude install libreadline6-dev libyaml-dev sqlite3 libgdbm-dev libncurses5-dev bison pkg-config libffi-dev

Uninstall RVM completely:

    rvm implode

After doing that also check for RVM references in `.bashrc`, `.bash_profile` and  `.profile` and remove or comment them out as needed.

#### Sundry RVM items.

Install Ruby via RVM:
	
	rvm install ruby-2.2.0

Reinstall Ruby via RVM:
	
	rvm reinstall ruby-2.2.0
	
Set the default Ruby version to use:
	
	rvm --default use 2.2.0

Reload Ruby via RVM:
	
	rvm reload

Get Ruby info via RVM:
	
	rvm info
	
Upgrade RVM to the latest, stable version:
	
	rvm get stable

Check the installed version of RVM:
	
	rvm -v
	
List Ruby versions available to RVM:
	
	rvm list
	
Install the latest version of Ruby GEMs:
	
	rvm rubygems latest
	
Install the latest version of Bundler:
	
	gem install bundler

***

*Ruby - Installing Ruby via RVM (c) by Jack Szwergold*

*This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*
