# Cheat Sheet - Ruby - Installing Ruby 2.0.0 from Source on Ubuntu 12.04

By Jack Szwergold, September 20, 2015

#### Installing Ruby 2.0.0 from source code.

First, run `aptitude update` like this:

    sudo aptitude update

Install the basics for the build:

    sudo aptitude install build-essential zlib1g-dev libssl-dev libreadline6-dev libyaml-dev

Get the latest Ruby 2.0.0 source code:

    wget http://cache.ruby-lang.org/pub/ruby/2.0/ruby-2.0.0-p598.tar.gz

Expand the source code archive:

    tar -xvzf ruby-2.0.0-p598.tar.gz

Go into the expanded directory:

    cd ruby-2.0.0-p598/

Run `configure` to configure the soucre:

    ./configure --prefix=/usr/local

Run `make` to compile the soucre:

    make

Now install it all:

    sudo make install

***

*Cheat Sheet - Ruby - Installing Ruby 2.0.0 from Source on Ubuntu 12.04 (c) by Jack Szwergold*

*This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*
