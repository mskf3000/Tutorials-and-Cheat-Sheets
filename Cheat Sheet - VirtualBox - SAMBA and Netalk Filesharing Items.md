# Cheat Sheet - VirtualBox - SAMBA and Netalk Filesharing Items

By Jack Szwergold, September 20, 2015

### SMB (Samba) installation setup on a VirtualBox guest OS.

 Install the SMB (Samba) and related items:

    sudo aptitude install samba libpam-smbpass cifs-utils smbfs

#### An example of a SMB (Samba) sharepoint for the `/var/www` directory.

Edit the `samba` config file:

	sudo nano /etc/samba/smb.conf
	
And add or adjust this stuff as needed:

	[global]
	   unix extensions = yes
	   load printers = no
	   printing = bsd
	   show add printer wizard = no
	   printcap name = /dev/null
	   disable spoolss = yes
	   unix extensions = no
	
	[apache_web_root]
	   path = /var/www
	   browsable = yes
	   read only = no
	   writable = yes
	   guest ok = no
	   veto files = /.DS_Store/
	   delete veto files = yes
	   ea support = yes
	   store dos attributes = no
	   create mask = 0664
	   directory mask = 0775
	
	[homes]
	   comment = Home Directories
	   browseable = no
	   read only = no
	   writable = yes
	   create mask = 0664
	   directory mask = 0775   
	   valid users = %S

Create a Samba user using this command:

	sudo smbpasswd -a testuser

Disable a Samba user:

	sudo smbpasswd -d testuser

Delete a Samba user:

	sudo smbpasswd -x testuser

List all Samba users:

    sudo pdbedit -L -v

If using `libpam-smbpass` then uncomment this line. Allows only PAM Unix users (users who have accounts on the server)

	# "security = user" is always a good idea. This will require a Unix account
	# in this server for every user accessing the server. See
	# /usr/share/doc/samba-doc/htmldocs/Samba3-HOWTO/ServerType.html
	# in the samba-doc package for details.
	   security = user

Test the Samba config:

	sudo testparm

Restart the Samba daemon:

    sudo service smbd restart

#### Sundry `netatalk` items.

Edit the `netatalk` config file:

	sudo nano /etc/netatalk/afpd.conf

Add this line to the end of the config file:

	- -tcp -noddp -uamlist uams_dhx.so,uams_dhx2_passwd.so -nosavepassword

Edit the `AppleVolumes` list:

    sudo nano /etc/netatalk/AppleVolumes.default

Add this line to the end of the `AppleVolumes.default` config file to define a `netatalk` mount point:

	# /var/www  WebRoot allow:@www-readwrite options:usedots,upriv
	/var/www  "WebRoot (Netatalk)" allow:@www-readwrite options:usedots,upriv,noadouble

#### Some Avahi daemon configs.

An example SMB (Samba) service (`samba.service`):

    sudo nano /etc/avahi/services/smb.service

	<?xml version="1.0" standalone='no'?><!--*-nxml-*-->
	<!DOCTYPE service-group SYSTEM "avahi-service.dtd">
	<service-group>
	  <name replace-wildcards="yes">%h</name>
	  <service>
	    <type>_smb._tcp</type>
	    <port>445</port>
	  </service>
	  <service>
	    <type>_device-info._tcp</type>
	    <port>0</port>
	    <txt-record>model=Macmini</txt-record>
	  </service>
	</service-group>

An example AFP (Apple Filing Protocol) service (`afpd.service`):

	sudo nano /etc/avahi/services/afpd.service
	
	<?xml version="1.0" standalone='no'?><!--*-nxml-*-->
	<!DOCTYPE service-group SYSTEM "avahi-service.dtd">
	<service-group>
	<name replace-wildcards="yes">%h</name>
	  <service>
	    <type>_afpovertcp._tcp</type>
	    <port>548</port>
	  </service>
	  <service>
	    <type>_device-info._tcp</type>
	    <port>0</port>
	    <txt-record>model=Macmini</txt-record>
	  </service>
	</service-group>

***

*Cheat Sheet - VirtualBox - SAMBA and Netalk Filesharing Items (c) by Jack Szwergold*

*This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*

