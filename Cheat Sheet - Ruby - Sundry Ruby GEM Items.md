# Cheat Sheet - Ruby - Sundry Ruby GEM Items

By Jack Szwergold, September 20, 2015

#### List all locally installed Ruby GEMs.

    gem query --local

#### Installing Ruby GEMs.

Ruby 1.9/1.8 method of installing a specific version of a Ruby GEM.

    sudo gem install [gem name here] --no-rdoc --no-ri --version=2.15.5

Ruby 2.0 method of installing a specific version of a Ruby GEM.

    sudo gem install [gem name here]:2.15.5 --no-rdoc --no-ri

#### Cleaning up old Ruby GEMs.

Will prompt you to choose which GEMs you want to remove.

    sudo gem uninstall [GEM name]

Will remove only version 1.1.9.

    sudo gem uninstall [GEM name] --version 1.1.9

Will remove all versions less than 1.3.4.

    sudo gem uninstall [GEM name] --version '<1.3.4'

Will remove all old versions of the GEM.

    sudo gem cleanup [GEM name]

***

*Cheat Sheet - Ruby - Sundry Ruby GEM Items (c) by Jack Szwergold*

*This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*
