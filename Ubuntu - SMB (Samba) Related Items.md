# Ubuntu - SMB (Samba) Related Items

By Jack Szwergold, September 15, 2015

#### The basics.

Then install SMB/CIFS stuff via `aptitude` like this:

	sudo aptitude install samba libpam-smbpass cifs-utils smbfs

The command to mount an SMB volume:
	
	sudo mount -t cifs //123.456.789.0/someuser /home/someotheruser/someotheruser_mount -o user="someuser",password="[password]",nounix,sec=ntlmssp,noperm,rw
	
The command to unmount an SMB volume:
	
	sudo umount //123.456.789.0/someuser

Check the running SAMBA status:

    smbstatus

#### Using an `.smbcredentials` file.

Edit `/etc/fstab` and add your entry:

	//server/share /pathto/mountpoint cifs credentials=/home/username/.smbcredentials,uid=shareuser,gid=sharegroup 0 0

Create the `.smbcredentials` file in your home directory:

	username=shareuser
	password=sharepassword
	domain=domain_or_workgroupname

Make sure you secure your `~/.smbcredentials` file:

    chmod 0600 ~/.smbcredentials

Finally, test the mount with:

    sudo mount -a

And you should be good to go!

#### Samba and veto files.

Setting `veto files` tells Samba to veto the creation or copying of any files that matches the `veto files`. Basic example here:

	; Veto any files containing the word Security,
	; any ending in .tmp, and any directory containing the
	; word root.
	veto files = /*Security*/*.tmp/*root*/

An example appropriate for Netatalk servers is here:

	; Veto the Apple specific files that a Netatalk server
	; creates.
	veto files = /.AppleDouble/.bin/.AppleDesktop/Network Trash Folder/

***

*Ubuntu - SMB (Samba) Related Items (c) by Jack Szwergold*

*This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*