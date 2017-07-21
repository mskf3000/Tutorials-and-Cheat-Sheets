## NodeJS - Installing NodeJS and NPM via a PPA on Ubuntu

By Jack Szwergold

#### Adding the NodeJS PPA to the system.

First install `python-software-properties` like this:

    sudo apt-get install python-software-properties

Next add the Node.js repository to the system like this:

    curl -sL https://deb.nodesource.com/setup_4.x | sudo bash - ;

Other versions can be set like this:

    curl -sL https://deb.nodesource.com/setup_6.x | sudo bash - ;
    curl -sL https://deb.nodesource.com/setup_5.x | sudo bash - ;
    curl -sL https://deb.nodesource.com/setup_4.x | sudo bash - ;
    curl -sL https://deb.nodesource.com/setup_0.12 | sudo bash - ;
    curl -sL https://deb.nodesource.com/setup_0.10 | sudo bash - ;

With that done, run `aptitude update` to get the new repository picked up like this:

    sudo aptitude update

Then install Node.js and NPM via `aptitude` like this:

    sudo aptitude install nodejs

Check the version number of Node.js like this:

    nodejs --version

And the output should be something like this:

    v0.10.40

Check the version number of NPM like this:

    npm --version

And the output should be something like this:

    1.4.28

***

*NodeJS - Installing NodeJS and NPM via a PPA on Ubuntu (c) by Jack Szwergold; written on September 22, 2015. This work is licensed under a Creative Commons Attribution-NonCommercial 4.0 International License (CC-BY-NC-4.0).*