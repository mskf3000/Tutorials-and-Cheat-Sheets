# Cheat Sheet - Bash

By Jack Szwergold, September 27, 2015

### Dumping Bash history

First remove the `.bash_history` file that holds past session history:

    rm ~/.bash_history

Then clear the history connected to the current Bash session:

    history -c

### Set Bash terminal colors.

#### CLICOLOR and related values.

Setting `CLICOLOR` sets the colors used by overall Bash settings. As explained in the manpage for `ls`:

> Use ANSI color sequences to distinguish file types. See `LSCOLORS` below. In addition to the file types mentioned in the `-F` option some extra attributes (setuid bit set, etc.) are also displayed. The colorization is dependent on a terminal type with the proper termcap(5) capabilities. The default `cons25` console has the proper capabilities, but to display the colors in an xterm(1), for example, the TERM variable must be set to `xterm-color`. Other terminal types may require similar adjustments. Colorization is silently disabled if the output isn't directed to a terminal unless the `CLICOLOR_FORCE` variable is defined.

To set `LSCOLORS` open up `.bash_profile`:

    nano ~/.bash_profile

And just set `CLICOLOR` settings like this:

    export CLICOLOR=1

#### LSCOLORS and related values.

Setting `LSCOLORS` sets the colors used by `ls` for directory listings. As explained in the manpage for `ls`:

> The value of this variable describes what color to use for which attribute when colors are enabled with `CLICOLOR`. This string is a concatenation of pairs of the format `fb`, where `f` is the foreground color and `b` is the background color. The color designators are as follows:
	
	a     black
	b     red
	c     green
	d     brown
	e     blue
	f     magenta
	g     cyan
	h     light grey
	A     bold black, usually shows up as dark grey
	B     bold red
	C     bold green
	D     bold brown, usually shows up as yellow
	E     bold blue
	F     bold magenta
	G     bold cyan
	H     bold light grey; looks like bright white
    x     default foreground or background

To set `LSCOLORS` open up `.bash_profile`:

    nano ~/.bash_profile

And here are some example `LSCOLORS` settings:

    export LSCOLORS=GxFxCxDxBxegedabagaced
    export LSCOLORS=ExGxBxDxCxEgEdxbxgxcxd

### An example of a basic Bash shell `.bash_profile`.

	# Setting CLI colors.
	export CLICOLOR=1
	
	# MAMP stuff.
	export MAMP_BIN=/Applications/MAMP/Library/bin
	export MAMP_PHP=/Applications/MAMP/bin/php/php5.4.10/bin
	
	# Git stuff.
	export GIT_BIN=/usr/local/git/bin
	
	# ImageMagick stuff.
	# export MAGICK_HOME="/opt/ImageMagick-6.9.1"
	export MAGICK_HOME="/opt/ImageMagick"
	export DYLD_LIBRARY_PATH="$MAGICK_HOME/lib/"
	
	# Final $PATH setting.
	# export PATH="$MAGICK_HOME/bin:/usr/local/bin:/usr/local/sbin:$GIT_BIN:$PATH"
	export PATH="$MAGICK_HOME/bin:$MAMP_BIN:$MAMP_PHP:/usr/local/bin:/usr/local/sbin:$GIT_BIN:$PATH"

***

*Cheat Sheet - Bash (c) by Jack Szwergold*

*This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*