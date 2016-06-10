## LDAP

By Jack Szwergold, October 5, 2015

Install `ldap-utils` via `aptitude` like this:

	sudo aptitude install ldap-utils

Run an LDAP search like this through the command line:

	ldapsearch -x -h ad.example.com -D "someuser@EXAMPLE.COM" -b "DC=someDC,DC=EXAMPLE,DC=COM" -W -L "sAMAccountName=some_user"

***

*LDAP (c) by Jack Szwergold. This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*