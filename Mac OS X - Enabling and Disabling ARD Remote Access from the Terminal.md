## Mac OS X - Enabling and Disabling ARD Remote Access from the Terminal

By Jack Szwergold

***

### Start ARD from the command line.

Sometimes ARD (Apple Remote Desktop) is deactivated, crashed or somehow doesn’t respond on a remote system. If you can get into the machine via SSH remote login, you can run this command from the Terminal to start ARD:

	sudo /System/Library/CoreServices/RemoteManagement/ARDAgent.app/Contents/Resources/kickstart -activate -configure -access -on -clientopts -setvnclegacy -vnclegacy yes -clientopts -setvncpw -vncpw mypasswd -restart -agent -privs -all

### Stop ARD from the command line.

And if you need to stop ARD on a remote machine via SSH remote login, just run this command:

	sudo /System/Library/CoreServices/RemoteManagement/ARDAgent.app/Contents/Resources/kickstart -deactivate -configure -access -off

***

*Mac OS X - Enabling and Disabling ARD Remote Access from the Terminal (c) by Jack Szwergold; written on September 13, 2015. This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*