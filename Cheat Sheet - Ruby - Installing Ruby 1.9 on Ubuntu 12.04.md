# Cheat Sheet - Ruby - Installing Ruby 1.9 on Ubuntu 12.04

By Jack Szwergold, September 20, 2015

By default, Ubuntu 12.04 will install Ruby 1.8 and not upgrade it. If your code needs the newer functionality available in Ruby 1.9 follow these steps to remove Ruby 1.8 and install Ruby 1.9 on Ubuntu 12.04.

#### Clean out the old Ruby 1.8 stuff.

First, uninstall the Ruby 1.8 GEMs like this:

	sudo gem1.8 list | cut -d" " -f1 | xargs sudo gem1.8 uninstall -aIx

Next, uninstall the core Ruby 1.8 stuff like this:

	sudo aptitude remove libruby1.8 ruby1.8 ruby1.8-dev rubygems1.8

Finally, do a check of installed packages on the Ubuntu 12.04 system to confirm Ruby 1.8 stuff has been removed:

	dpkg --get-selections | grep ruby

That list should be empty, but if you see something like this listed:

    libruby1.8

Don’t worry. That’s just a system library Ubuntu 12.04 has sitting around for reference—I believe—and has nothing to do with the core core Ruby 1.8 stuff that was just removed.

#### Installing Ruby 1.9 on Ubuntu 12.04.

First, run `aptitude update` like this:

    sudo aptitude update

With that done, install the core Ruby 1.9 stuff like this:
	
    sudo aptitude install ruby1.9.1 ruby1.9.1-dev \
	                      rubygems1.9.1 irb1.9.1 ri1.9.1 rdoc1.9.1 \
	                      build-essential libopenssl-ruby1.9.1 \
	                      libssl-dev zlib1g-dev
		
Now, run `update-alternatives` to get the new Ruby 1.9 properly recognized and linked on the system:

	sudo update-alternatives --install /usr/bin/ruby ruby /usr/bin/ruby1.9.1 400 \
	         --slave   /usr/share/man/man1/ruby.1.gz ruby.1.gz \
	                        /usr/share/man/man1/ruby1.9.1.1.gz \
	        --slave   /usr/bin/ri ri /usr/bin/ri1.9.1 \
	        --slave   /usr/bin/irb irb /usr/bin/irb1.9.1 \
	        --slave   /usr/bin/rdoc rdoc /usr/bin/rdoc1.9.1


Next, run `update-alternatives` again to choose your Ruby interpreter. This also changes symbolic links for core Ruby items:

    sudo update-alternatives --config ruby

Make your selection and then move onto the `update-alternatives` for related Ruby GEMs:

    sudo update-alternatives --config gem

Finally, confirm the version of Ruby has been updated like this:
		
     ruby --version

***

*Cheat Sheet - Ruby - Installing Ruby 1.9 on Ubuntu 12.04 (c) by Jack Szwergold*

*This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*
