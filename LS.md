## LS

By Jack Szwergold, September 17, 2015

List one item per line:

    ls -1

List items in a one line, comma separated list format:

    ls -m

List all of the items in the directory, order it by time and reverse the list:

    ls -latr

List all of the items in the directory in natural order and in reverse:

    ls -lavr

Do a directory search in reverse and find the last file with the `.sh` in the filname; can be changed to match anything else in the filename:

    ls -lrt | awk '/.sh/ { filename=$NF }; END { print filename }'

List only directories:

    ls -l | egrep '^d'

List only files:

    ls -l | egrep -v '^d'

Get a count of files (not hidden) in a folder:

    ls -1 | wc -l

***

<sup>*LS (c) by Jack Szwergold. This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*</sup>