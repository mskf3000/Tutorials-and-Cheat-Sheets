# Cheat Sheet - Ruby - Installing Ruby from Source on Ubuntu

By Jack Szwergold, September 21, 2015

This tutorial explains how to compile Ruby version 2.2.3 from source on Ubuntu, but the same basic recipe can be used for most any other version of Ruby on Ubuntu.

#### Installing Ruby from source code.

First, run `aptitude update` like this:

    sudo aptitude update

Install the basics for the build:

    sudo aptitude install build-essential zlib1g-dev libssl-dev libreadline6-dev libyaml-dev

First grab a compressed archive from an official `ruby` source site:

    curl -O -L http://cache.ruby-lang.org/pub/ruby/2.2/ruby-2.2.3.tar.gz

Next, decompress the archive like this:

    tar -xf ruby-2.*.tar.gz

Now go into the decompressed directory:

    cd ruby-2.*

Run this `configure` command:

    ./configure --prefix=/usr/local

Once the `configure` process completes, run `make`:

    make

After that’s done, let’s test the `make` build:

    make test

Finally install it by running `sudo make install`:

    sudo make install

And once it’s installed, run the command with `-h` to do a simple check to see it’s working:

    ruby --version

The output should be something like this:

> ruby 2.2.3p173 (2015-08-18 revision 51636) [x86_64-linux]

When all is said and done, let’s install Bundler, since that is the one Ruby GEM lots of Ruby applictaions like to use:

    sudo gem install bundler

#### Uninstalling Ruby from source code.

If you need to uninstall Ruby if it was installed from source, just do this.

First, let’s uninstall two Ruby GEMs I usually like to install on a system:

    sudo gem uninstall bundler passenger

With taht done, go into the source directory like this:

    cd ~/ruby-2.*

Now run this command to get rid of the core Ruby stuff:

    sudo make uninstall

And let’s clean out the Ruby GEMs:

    sudo rm -rf /usr/local/lib/ruby/

Toss the source directory by going back to the home directory:

    cd ~/

And then toss that directory like this:

    rm -rf ~/ruby-2.*

#### Fix for WEBrick (web server) slowness.

For some reason WEBrick—the default Ruby HTTP server toolkit—can be slow in development environments. This appears to stem from slow reverse DNS lookups which are not really needed. So we can disable that by doing this. Open up the `config.rb` for WEBrick like this:

    sudo nano /usr/local/lib/ruby/2.2.3/webrick/config.rb

Find this line:

    :DoNotReverseLookup => nil,

And change it to `true` like this:

    :DoNotReverseLookup => true,

***

*Cheat Sheet - Ruby - Installing Ruby from Source on Ubuntu (c) by Jack Szwergold*

*This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*
