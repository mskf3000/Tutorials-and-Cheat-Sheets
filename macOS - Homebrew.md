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
      htop nload mtr wget watch rsync jq \
      geoip geoipupdate \
      iftop iperf nmap \
      lame ffmpeg id3v2 \
      imagemagick exiftool potrace \
      figlet fortune \
      sdl sdl2 \
      libdvdcss;

***

If you get a package that spits out a message like “Error: An unsatisfied requirement failed this build.” but you need to force the install, just add the `--ignore-dependencies` option. For example, to force Htop to install like this just run this command:

	brew install htop --ignore-dependencies

***

Uninstall all Homebrew pacakages:

    brew remove --force --ignore-dependencies $(brew list);

Or do a similar thing this way:

	brew list -1 | xargs brew uninstall -f

***

Install Java via Homebrew like this:

	brew cask install caskroom/cask/java

Or install a specific version like this:

	brew cask install caskroom/versions/java8

Uninstall it like this:

	brew cask uninstall java8

***

*macOS - Homebrew (c) by Jack Szwergold; written on December 27, 2016. This work is licensed under a Creative Commons Attribution-NonCommercial 4.0 International License (CC-BY-NC-4.0).*