# Screen

By Jack Szwergold, September 27, 2015

***

Install `screen`:

    sudo aptitude install screen

Create a screen named `test_screen`.

    screen -S test_screen

List all open screens.

    screen -ls

The output would be.

	There is a screen on:
	        2839.test_screen        (09/27/2015 02:48:00 AM)        (Attached)
	1 Socket in /var/run/screen/S-sysop.

So if you wanted to reattach to that session, just enter this line.

	screen -D -r '2839.test_screen'

The `-D` detaches the screen session and then the `-r` reattaches the session.

***

*Screen (c) by Jack Szwergold*

*This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*