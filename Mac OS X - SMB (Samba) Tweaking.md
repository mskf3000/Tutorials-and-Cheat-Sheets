# Mac OS X - SMB (Samba) Tweaking

By Jack Szwergold, October 6, 2015

Honestly, some of this is just “magic” and I don’t trust any of these configs. But noting them here just in case I ever have to enter the nightmare of Mac/Windows cross platform nonsense.

#### A bad idea.

    sudo nano /etc/nsmb.conf

	[default]
	    streams=no
	    notify_off=yes
	    port445=no_netbios

#### A better idea.

    sudo nano /etc/nsmb.conf

	[default]
	    streams=yes

#### Something that might work.

	sudo nano /etc/smb.conf
	
	sudo nano /private/etc/smb.conf

	[global]
	    socket options = TCP_NODELAY IPTOS_LOWDELAY
	    large readwrite = no

#### Edit SMB (Samba) shares.

	sudo nano /var/db/samba/smb.shares

***

*Mac OS X - SMB (Samba) Tweaking (c) by Jack Szwergold*

*This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*