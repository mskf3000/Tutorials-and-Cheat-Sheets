## macOS - Disabling FortiClient from Startup on macOS

By Jack Szwergold

To stop FortiClient from loading on reboot after it’s been installed, edit the contents of these two PLIST (Property List) files:

	com.fortinet.forticlient.fct_launcher.plist
	com.fortinet.forticlient.credential_store.plist

It breaks down like this:

## For `fct_launcher.plist`.

Open up the `fct_launcher.plist` file like this:

	sudo nano /Library/LaunchAgents/com.fortinet.forticlient.fct_launcher.plist

The contents of the `fct_launcher.plist` file should look like this:

	<?xml version="1.0" encoding="UTF-8"?>
	<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
	<plist version="1.0">
	<dict>
	        <key>Label</key>
	        <string>com.fortinet.fct_launcher</string>
	        <key>Program</key>
	        <string>/Library/Application Support/Fortinet/FortiClient/bin/FCTLauncher</string>
	        <key>RunAtLoad</key>
	        <true/>
	</dict>
	</plist>

Change `<true/>` at the bottom of the list to `<false/>` and save the file.

## For `credential_store.plist`.

Open up the `forticlient.credential_store.plist` file like this:

    sudo nano /Library/LaunchAgents/com.fortinet.forticlient.credential_store.plist

The contents of the `forticlient.credential_store.plist` file should look like this:

	<?xml version="1.0" encoding="UTF-8"?>
	<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
	<plist version="1.0">
	<dict>
	        <key>Label</key>
	        <string>com.fortinet.credential_store</string>
	        <key>Program</key>
	        <string>/Library/Application Support/Fortinet/FortiClient/bin/CredentialStore</string>
	        <key>RunAtLoad</key>
	        <true/>
	        <key>KeepAlive</key>
	        <dict>
	                <key>SuccessfulExit</key>
	                <false/>
	        </dict>
	        <key>ThrottleInterval</key>
	        <integer>0</integer>
	        <key>ProcessType</key>
	        <string>Interactive</string>
	</dict>
	</plist>    

Look for the key that reads `<key>RunAtLoad</key>` and set the `<true/>` right beneath it to `<false/>` and save the file.

Now, with both files modified and saved, reboot your Mac and the FortiClient should no longer be automatically loaded on reboot. To start it manually, go to “Applications” and double-click it like any other app.

***

*macOS - Disabling FortiClient from Startup on macOS (c) by Jack Szwergold; written on March 24, 2022. This work is licensed under a Creative Commons Attribution-NonCommercial 4.0 International License (CC-BY-NC-4.0).*