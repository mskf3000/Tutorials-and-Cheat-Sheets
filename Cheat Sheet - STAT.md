# Cheat Sheet - STAT

By Jack Szwergold, September 15, 2015

#### Using `stat` in Mac OS X.

**ACCESS:** Get the last access time of a file.

    stat -f "%Sa" -t "%Y-%m-%d %H:%M:%S" filename_here.txt

**MODIFICATION:** Get the modification time of a file.

    stat -f "%Sm" -t "%Y-%m-%d %H:%M:%S" filename_here.txt

**CHANGED:** Get the modification time of a file.

    stat -f "%Sc" -t "%Y-%m-%d %H:%M:%S" filename_here.txt

**BIRTH:** Get the birth time of an inode.

    stat -f "%SB" -t "%Y-%m-%d %H:%M:%S" filename_here.txt

**FILESIZE:** Get the size of a file.

    stat -f "%z" filename_here.txt

#### Using `stat` in Linux/Unix.

**ACCESS:** Get the last access time of a file.

	stat -c '%.19x' filename_here.txt
	stat -c '%x' filename_here.txt | sed 's/\(:[0-9]\{2\}\)\.[0-9]* /\1 /'

**MODIFICATION:** Get the modification time of a file.

	stat -c '%.19y' filename_here.txt
	stat -c '%y' filename_here.txt | sed 's/\(:[0-9]\{2\}\)\.[0-9]* /\1 /'

**CHANGED:** Get the modification time of a file.

	stat -c '%.19z' filename_here.txt
	stat -c '%z' filename_here.txt | sed 's/\(:[0-9]\{2\}\)\.[0-9]* /\1 /'

**BIRTH:** Get the birth time of an inode.

	stat -c '%.19w' filename_here.txt
	stat -c '%w' filename_here.txt | sed 's/\(:[0-9]\{2\}\)\.[0-9]* /\1 /'

**FILESIZE:** Get the size of a file.

	stat -c "%s" filename_here.txt

***

*Cheat Sheet - STAT (c) by Jack Szwergold*

*This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*