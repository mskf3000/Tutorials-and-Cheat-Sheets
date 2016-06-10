## Mac OS X - Some GlobalPreferences Related Items

### Check a list of all user connected preference files.

List all Mac OS X user defaults preference files:

    defaults domains

List all Mac OS X user defaults preference files, more readable format:

    defaults domains | tr -s ', ' '\n'

### Archiving the `.GlobalPreferences.plist`.

Save the `.GlobalPreferences.plist` in a human readable format:

	defaults read > ~/GlobalPreferences.plist.txt
	
### Removing the `.GlobalPreferences.plist`.

First, make backup a copy of your `GlobalPreferences.plist` to your user’s directory:

	cp -fp ~/Library/Preferences/.GlobalPreferences.plist ~/GlobalPreferences.plist
	
Now, delete the original `.GlobalPreferences.plist` file:

    rm -f ~/Library/Preferences/.GlobalPreferences.plist

And then, just restart the Finder with this command:

    killall Finder

### Restoring the `.GlobalPreferences.plist`.

If you need to, restore the backup copy of `.GlobalPreferences.plist` like this:

    mv -f ~/GlobalPreferences.plist ~/Library/Preferences/.GlobalPreferences.plist

And then, just restart the Finder with this command:

    killall Finder

***

*Mac OS X - Some GlobalPreferences Related Items (c) by Jack Szwergold. This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*