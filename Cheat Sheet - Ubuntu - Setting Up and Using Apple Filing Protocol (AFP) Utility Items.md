# Cheat Sheet - Ubuntu - Setting Up and Using Apple Filing Protocol (AFP) Utility Items

By Jack Szwergold, October 4, 2015

#### Installation.

Install the Apple Filing Protocol (AFP) stuff:

    sudo aptitude install afpfs-ng-utils

#### Mounting and unmounting an AFP volume.

Mounting an AFP volume:

    mount_afp afp://user:password@192.168.1.101/sharename /media/AFP

The overall structure of how to mount an AFP volume:

    afp_client mount -u [user_name] -p [password] [server]:[volume] [mountpoint]

Unmount an AFP volume:

    afp_client unmount [mountpoint]

***

*Cheat Sheet - Ubuntu - Setting Up and Using Apple Filing Protocol (AFP) Utility Items (c) by Jack Szwergold*

*This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*