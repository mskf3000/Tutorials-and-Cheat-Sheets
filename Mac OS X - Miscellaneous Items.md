## Mac OS X - Miscellaneous Items

By Jack Szwergold, October 6, 2015

### ARP related stuff.

List all Arp entries on the system:

    arp -a

List all Arp entries on the system and show link-layer reachability info:

    arp -al

List all Arp entries on the system and show extended link-layer reachability info:

    arp -alx

### What are `.SM.gul.` files.

If you are on a system and see files that a prefaced by `.SM.gul.` clone files like this:

	/Library/ColorSync/Profiles/.SM.gul.WebSafeColors.icc
	/Library/ColorSync/Profiles/WebSafeColors.icc

Those are files created by Apple’s Migration Assistant. Either the process forgot to clean up the `.SM.gul.` files or it crashed and left them behind. But those `.SM.gul.` files can be safely ignored or deleted if need bee.

### Locations of where Mac OS X stores user & group information.

Running this `ls -la` command:

	sudo ls -la /private/var/db/dslocal/nodes/Default

Will show you and overview of the `/private/var/db/dslocal/nodes/Default`:

	drw-------   12 root  wheel     408 Jan 25  2015 .
	drwxr-xr-x    3 root  wheel     102 Aug 24  2013 ..
	drwx------   10 root  wheel     340 Jan 25  2015 aliases
	drwx------    3 root  wheel     102 Jan 25  2015 computers
	drwx------    4 root  wheel     136 Jan 25  2015 config
	drwx------  102 root  wheel    3468 Jan 25  2015 groups
	drwx------    3 root  wheel     102 Jan 25  2015 networks
	drwx------    3 root  wheel     102 Jan 25  2015 sharepoints
	-rw-------    1 root  wheel  352256 Oct  1 00:20 sqlindex
	-rw-------    1 root  wheel   32768 Oct  6 23:42 sqlindex-shm
	-rw-------    1 root  wheel  350232 Oct  6 20:49 sqlindex-wal
	drwx------   79 root  wheel    2686 Oct  6 20:49 users

This `ls -la` command will show you the `.plist` files for users on the system:

	sudo ls -la /private/var/db/dslocal/nodes/Default/users
	
This `ls -la` command will show you the `.plist` files for groups on the system:

	sudo ls -la /private/var/db/dslocal/nodes/Default/groups

### Kill `VNCDragHelper` if it spirals out of control.

Sometimes the VNC server on a remote server can spiral out of control and make VNC access impossible. The solution is to SSH into that server and run this command:

    sudo killall -9 VNCDragHelper

### Checking library framework type and architecture.

To check what the framework type and architecture of a file is, just run this `lipo` command:

    lipo -info /path/to/file/you/wish/to/check

For example, check the architecture type of the `AppleIntermediateCodec` like this:

    lipo -info /Library/QuickTime/AppleIntermediateCodec.component/Contents/MacOS/AppleIntermediateCodec

The output would be something like this:

    Non-fat file: /Library/QuickTime/AppleIntermediateCodec.component/Contents/MacOS/AppleIntermediateCodec is architecture: i386

### Check recordable media type from the command line

#### The `system_profiler` method.

Get all info on the recordable media drive and inserted media:

    system_profiler SPDiscBurningDataType

Parse out just the media info with `sed`:

    system_profiler SPDiscBurningDataType | sed -n "/Type:/,/*/ p"

#### The `diskutil info` method.

Assuming `diskutil list` gives us `/dev/disk3` for the recorable media, this gives you info on the drive and the inserted media:

    diskutil info /dev/disk3

And this would eject the media from `/dev/disk3`:

    diskutil info /dev/disk3

### How go rebuild Spotlight indexes.

Force Spotlight to reindex a specific volume:

	sudo rm -R /.Spotlight-V100
	sudo mdutil -i on /
	sudo mdutil -E /

Force Spotlight to reindex Apple Mail:

    mdimport ~/Library/Mail

#### Update the Systems `locate` database.

Use this command to force Spotlight to reindex the whole system:

    sudo /usr/libexec/locate.updatedb

Or just go into `/usr/libexec`:

	cd /usr/libexec

And then run `./locate.updatedb` from there:

	sudo ./locate.updatedb

### Some basic networking items.

Display Bonjour/Multicast services from the command line.

    dns-sd -B

Get the network interface parameters:

    ifconfig

Detect listening network ports:

    netstat -tuln

Detect listening network ports on Mac OS X

    sudo lsof -i -P | grep -i "listen"

### Enable and disable Bonjour in Mac OS X 10.9

Disable Bonjour with `launchctl` like this:

    sudo launchctl unload -w /System/Library/LaunchDaemons/com.apple.mDNSResponder.plist
    sudo launchctl unload -w /System/Library/LaunchDaemons/com.apple.mDNSResponderHelper.plist

Re-enable Bonjour with `launchctl` like this:

    sudo launchctl load -w /System/Library/LaunchDaemons/com.apple.mDNSResponder.plist
    sudo launchctl load -w /System/Library/LaunchDaemons/com.apple.mDNSResponderHelper.plist

### Clear out the DNS cache in different versions of Mac OS X.

Mac OS X 10.10 (Yosemite):

    sudo dscacheutil -flushcache
    sudo killall -HUP mDNSResponder

Mac OS X 10.7, 10.8 and 10.9 (Lion, Mountain Lion, Mavericks):

    sudo killall -HUP mDNSResponder

Mac OS X 10.6.8 (Snow Leopard):

    dscacheutil -flushcache

Pre Mac OS X 10.5 (Leopard):

    lookupd --flushcache

### Clean up the Mac OS X Mail index.

Use this SQLite command in Mac OS X 10.7, 10.8 and 10.9 (Lion, Mountain Lion, Mavericks):

    sqlite3 ~/Library/Mail/V2/MailData/Envelope\ Index vacuum

Use this SQLite command in Mac OS X 10.6 (Snow Leopard):

    /usr/bin/sqlite3 ~/Library/Mail/Envelope\ Index vacuum

### Clean up the iPhoto database.

Go into the `iPhoto Library` directory:

    cd ~/Pictures/iPhoto\ Library

And run this Bash script to get SQLite to do it’s thing:

    for dbase in *.db; do sqlite3 $dbase "vacuum;"; done

### Disable Time Machine asking about new disks.

The basic command:

    defaults write com.apple.TimeMachine DoNotOfferNewDisksForBackup -boolean YES

The command running as `sudo` with the full preference file path:

    sudo defaults write /Library/Preferences/com.apple.TimeMachine DoNotOfferNewDisksForBackup -bool YES

### Restart the ARD (Apple Remote Desktop) client from the command line.

Useful for cases when you can SSH into a server but somehow remote login services via ARD or VNC have crashed or aren’t started:

    sudo /System/Library/CoreServices/RemoteManagement/ARDAgent.app/Contents/Resources/kickstart -restart -agent

### Disable `ApplePersistence`.

The `ApplePersistence` setting controls the behavior when apps open up on automatically on reboot. You can disable it like this:

    defaults write -g ApplePersistence -bool no

Note this might disable versioning for documents saved from some applications, but if that is not an issue just feel free to disable it.

### Setting a ComputerName, HostName and LocalHostName in Mac OS X.

You can check the values of ComputerName, HostName and LocalHostName using `scutil --get` like this:

    scutil --get ComputerName
    scutil --get HostName
    scutil --get LocalHostName

And you can set those values using `scutil --set` like this:

    sudo scutil --set ComputerName 'yourHostName'
    sudo scutil --set HostName 'yourHostName'
    sudo scutil --set LocalHostName  'yourHostName'

### Tell Mac OS X not to write `.DS_Store` files to network shares.

Check the value of `DSDontWriteNetworkStores` like this:

    defaults read com.apple.desktopservices DSDontWriteNetworkStores

Set the value of `DSDontWriteNetworkStores` like this:

    defaults write com.apple.desktopservices DSDontWriteNetworkStores true

### Fix font smoothing in Mac OS X for non-Apple monitors.

Mac OS X won’t do a good job of dealing with LC font smoothing if you use non-Apple monitors. So run this command to force a new font smoothing value:

    defaults -currentHost write -globalDomain AppleFontSmoothing -int 2

### Accessing System Profiler data from the command line.

Get all of the Mac OS X system profile info from the command line like this:

    system_profiler

Use this command to specifically get the Mac OS X system version information from the command line:

    system_profiler SPSoftwareDataType

The output of that command would be something like this:

	System Software Overview:
	
	  System Version: OS X 10.9.5 (13F1112)
	  Kernel Version: Darwin 13.4.0
	  Boot Volume: Hard Disk
	  Boot Mode: Normal
	  Computer Name: Macintosh
	  User Name: Some User (some_user)
	  Secure Virtual Memory: Enabled
	  Time since boot: 6:54

### How to disable disk journaling on HFS+ volumes from the command line.

The way to handle this from Disk Utility is to select the volume in question, hold down the “Option” key and select “File" menu. The “Disable Journaling” will appear. Or from the command line, just do this:

    diskutil disableJournal /Volumes/NameOfTheVolumeHere

### How to install basic Mac OS X developer command line tools instead of the full Xcode package.

If you are simply going to compile items from the command line, you don’t need to install the full Xcode package to get things up and running anymore. Instead open up a Terminal window and just type in this command:

    xcode-select --install

A window will pop up with a few different choices. Just choose “Install” (don’t click “Get Xcode”; see picture below) and let the process complete. When done you will have all of the necessary command line tools you need to compile source code in the Terminal in Mac OS X.


### How to force Mac OS X to use a specific hostname instead of a DHCP provided hostname.

Sometimes a DHCP server on a network will force it’s own oddball hostname onto a Mac OS X machine that has connected to it. This would override the local machine name and might cause problems/confussion. If you don’t need the DHCP provided hostname and would like to force your own hostname, just use this command:

    sudo scutil --set HostName [hostname]

Exit the Terminal and open it up again and all should be good and the hostname in the command prompt should be permanently set to the `[hostname]` you set with the above `scutil` command.

***

*Mac OS X - Miscellaneous Items (c) by Jack Szwergold. This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*