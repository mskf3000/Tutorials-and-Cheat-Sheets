## macOS - Homebrew

By Jack Szwergold

Install Homebrew like this:

    /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"

***

Run this to make sure the `/usr/local/bin` and `/usr/local/share` directories are owned by you to get this stuff installed:

    sudo chown -R `whoami`:admin /usr/local/{bin,share}

And run this command to get NMAP—and other Python dependent items—installed properly:

    sudo chown -R `whoami`:admin /usr/local/lib/python2.7/

***

A basic pile of macOS command line tools I like to install:

    brew install git autoconf automake libtool \
      htop nload mtr wget \
      geoip geoipupdate \
      iftop iperf exiftool nmap \
      lame ffmpeg id3v2 \
      figlet fortune;

***

Uninstall all Homebrew pacakages:

    brew remove --force --ignore-dependencies $(brew list);

***

*macOS - Homebrew (c) by Jack Szwergold; written on December 27, 2016. This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*