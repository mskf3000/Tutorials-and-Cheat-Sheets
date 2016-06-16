## Mac OS X - SheepShaver Emulator Related Items

By Jack Szwergold

Mac OS X 10.9.5 and higher won’t allow Sheepsaver to access optical drives unless the user who is running Sheepsaver is added as a member of the group `operator` which apparently is the group that deals optical disk volumes.

To check if your user is already a member of the group `operator` use this command:

    dscacheutil -q group -a name operator

If your user is not already a member of the group `operator`, run this command to add them to the group:

    sudo dscl . -append /Groups/operator GroupMembership $USER

And to remove your user from the group `operator` just run this command:

	sudo dscl . -delete /Groups/operator GroupMembership $USER

***

*Mac OS X - SheepShaver Emulator Related Items (c) by Jack Szwergold; written on October 9, 2015. This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*
