# Wget

By Jack Szwergold, October 8, 2015

***

#### Some Wget command parameters.

- `-A [list/pattern]`: Accept list. Basically a comma separated list of filename suffixes to accept. For example, `-A jpg,jpeg,png,gif` would grab all image files and `-A mp3` would grab all MP3 files.
- `-R [list/pattern]`: Reject list. The opposite of the accept list. Allows a command to reject a list of items.
- `-r`: Turn on recursive retrieving.
- `-l [number]`: Tells the command to go down `[number]` levels; so `-l 2` would be two levels. Set it to `-l inf` for infinite recursion.
- `-nd`: Do not create a hierarchy of directories when retrieving recursively.
- `-nc`: No clobber. Which basically means do not download the same content if it’s already been downloaded.
- `-np`: No parent directories. Do not ever ascend to the parent directory when retrieving recursively.
- `-p`: This option causes Wget to download all the files that are necessary to properly display a given HTML page. This includes such things as inlined images, sounds, and referenced stylesheets.
- `-w`: Wait the specified number of seconds between the retrievals. `-w 1` would wait 1 second between retrievals.
- `-e`: Execute a Wget command. Such as `-e robots=off`.
- `-k`: Convert links. After the download is complete, convert the links in the document to make them suitable for local viewing.
- `-H`: Enable spanning across hosts when doing recursive retrieving.
- `-N`: Turn on time-stamping.
- `-m`: Turn on options suitable for mirroring. This option turns on recursion and time-stamping, sets infinite recursion depth and keeps FTP directory listings. It is currently equivalent to `-r -N -l inf --no-remove-listing`.

#### Some basic Wget command examples.

A basic mirroring command example:

	wget -m -k -p -w 1 -e robots=off http://[some url]

Another basic mirroring command example:

	wget --adjust-extension --span-hosts --convert-links --backup-converted --page-requisites http://[some url]

The same basic command, but this spans hosts with `-H`; be careful with this or else your computer might attempt to download the whole Internet:

	wget -m -k -p -w 1 -H -e robots=off http://[some url]

Grab a site’s content 3 levels down:

	wget -N -r -l 3 -p -np -k http://[some url]

Grab all JPG, JPEG, PNG and GIF assets off of a site:

	wget -N -r -l 3 -p -np -A jpg,jpeg,png,gif http://[some url]

Grab all JPG, JPEG, PNG and GIF assets off of a site and span hosts if needed:

	wget -N -r -l 3 -p -np -H -A jpg,jpeg,png,gif http://[some url]

***

*Wget (c) by Jack Szwergold*

*This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*