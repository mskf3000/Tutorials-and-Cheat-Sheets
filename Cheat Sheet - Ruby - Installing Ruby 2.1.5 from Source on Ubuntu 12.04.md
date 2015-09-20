# Cheat Sheet - Ruby - Installing Ruby 2.1.5 from Source on Ubuntu 12.04

By Jack Szwergold, September 20, 2015

#### Installing Ruby 2.1.5 from source code.

First, run `aptitude update` like this:

    sudo aptitude update

Install the basics for the build:

    sudo aptitude install build-essential zlib1g-dev libssl-dev libreadline6-dev libyaml-dev

Get the latest Ruby source code:

    wget http://cache.ruby-lang.org/pub/ruby/2.1/ruby-2.1.5.tar.gz

Expand the source code archive:

    tar -xvzf ruby-2.1.5.tar.gz

Go into the expanded directory:

    cd ruby-2.1.5/

Run `configure` to configure the soucre:

    ./configure --prefix=/usr/local

Run `make` to compile the soucre:

    make

Let’s test the build:

    make test

All good? Great! Install it all:

    sudo make install

And let’s install Bundler, since that is the one Ruby GEM lots of Ruby applictaions like to use:

    sudo gem install bundler

#### Uninstalling Ruby 2.1.5 from source code.

If you need to uninstall Ruby if it was installed from source, just do this.

First, let’s uninstall two Ruby GEMs I usually like to install on a system:

    sudo gem uninstall bundler passenger

With taht done, go into the source directory like this:

    cd ~/ruby-2.1.5

Now run this command to get rid of the core Ruby stuff:

    sudo make uninstall

And let’s clean out the Ruby GEMs:

    sudo rm -rf /usr/local/lib/ruby/

Toss the source directory by going back to the home directory:

    cd ~/

And then toss that directory like this:

    rm -rf ~/ruby-2.1.5

#### Fix for WEBrick (web server) slowness.

For some reason WEBrick—the default Ruby HTTP server toolkit—can be slow in development environments. This appears to stem from slow reverse DNS lookups which are not really needed. So we can disable that by doing this. Open up the `config.rb` for WEBrick like this:

    sudo nano /usr/local/lib/ruby/2.1.0/webrick/config.rb

Find this line:

    :DoNotReverseLookup => nil,

And change it to `true` like this:

    :DoNotReverseLookup => true,

***

*Cheat Sheet - Ruby - Installing Ruby 2.1.5 from Source on Ubuntu 12.04 (c) by Jack Szwergold*

*This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*
