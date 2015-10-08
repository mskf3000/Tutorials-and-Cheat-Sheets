# Cheat Sheet - Tar

By Jack Szwergold, October 8, 2015

Run this command to show a list of a Tar/Gzipped files contents:

    tar -ztvf [tar archive]

For example, running that command on `fortune-0.2.tar.gz` like this:

	tar -ztvf fortune-0.2.tar.gz

Would show the following content info:

	drwxr-xr-x  0 leitner users       0 Oct 25  1998 fortune-0.2/
	-rw-r--r--  0 leitner users     429 Oct 25  1998 fortune-0.2/fortune.lsm
	-rw-r--r--  0 leitner users    5179 Oct 24  1998 fortune-0.2/fortune.c
	-rw-r--r--  0 leitner users     205 Mar  9  1998 fortune-0.2/Makefile
	-rw-r--r--  0 leitner users     787 Mar  9  1998 fortune-0.2/README
	drwxr-xr-x  0 leitner users       0 Oct 25  1998 fortune-0.2/debian/
	-rw-r--r--  0 leitner users     294 Oct 24  1998 fortune-0.2/debian/changelog
	-rw-r--r--  0 leitner users     764 Oct 24  1998 fortune-0.2/debian/copyright
	-rw-r--r--  0 leitner users     474 Oct 24  1998 fortune-0.2/debian/control
	-rwxr-xr-x  0 leitner users    1808 Oct 24  1998 fortune-0.2/debian/rules
	-rw-r--r--  0 leitner users     514 Oct 24  1998 fortune-0.2/debian/fortune.6
	-rw-r--r--  0 leitner users     286 Oct 25  1998 fortune-0.2/CHANGES

***

*Cheat Sheet - Tar (c) by Jack Szwergold*

*This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*