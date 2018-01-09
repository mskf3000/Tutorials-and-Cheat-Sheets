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

    brew install git autoconf automake libtool ncurses \
      htop nload mtr wget watch rsync \
      geoip geoipupdate \
      iftop iperf nmap \
      lame ffmpeg id3v2 \
      imagemagick exiftool potrace \
      figlet fortune \
      sdl sdl2 \
      libdvdcss;

***

Uninstall all Homebrew pacakages:

    brew remove --force --ignore-dependencies $(brew list);

Or do a similar thing this way:

	brew list -1 | xargs brew uninstall -f

***

*macOS - Homebrew (c) by Jack Szwergold; written on December 27, 2016. This work is licensed under a Creative Commons Attribution-NonCommercial 4.0 International License (CC-BY-NC-4.0).*