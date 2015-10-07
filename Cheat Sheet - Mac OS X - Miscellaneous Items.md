# Cheat Sheet - Mac OS X - Miscellaneous Items

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

***

*Cheat Sheet - Mac OS X - Miscellaneous Items (c) by Jack Szwergold*

*This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*