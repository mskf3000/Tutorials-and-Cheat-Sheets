# Using an Ubuntu Linux Server

By Jack Szwergold, March 19, 2014

## Part 1: Configuring a Base Level Ubuntu Server

The purpose of this tutorial is to explain how I like to configure a base level Ubuntu server. This doesn’t go into details as to how you got Ubuntu installed on a server to begin with. But begins with the assumption that you have an Ubuntu machine set up to the point you have a basic terminal prompt and you can login as a user with administrator rights.

### Table of contents.

- [Be sure to install ‘aptitude’ first.](ubuntu_server_usage_part_1#aptitude)
- [Disable the ‘root’ account.](ubuntu_server_usage_part_1#disable_root)
- [Create the users.](ubuntu_server_usage_part_1#create_users)
- [Add the new sysop user to the ‘sudoers’ file.](ubuntu_server_usage_part_1#add_sysop_to_sudoers)
- [Create the ‘www-readwrite’ group.](ubuntu_server_usage_part_1#create_wwwreadwrite)
- [Adjust default ‘umask’ for group writability.](ubuntu_server_usage_part_1#adjust_default_umask)
- [Set the server time and related timezone info.](ubuntu_server_usage_part_1#set_time_and_timezone)
- [Ensure that the ‘locale’ settings are correct.](ubuntu_server_usage_part_1#locale_settings)
- [Edit the ‘sources.list’ to enable partner package updates.](ubuntu_server_usage_part_1#enable_partner_pacakages)
- [Install base level tools and utilities.](ubuntu_server_usage_part_1#install_base_level_tools)
- [Install compilers, GIT, SVN and general development related stuff.](ubuntu_server_usage_part_1#install_development_tools)
- [Install and initialize the ‘locate’ tool.](ubuntu_server_usage_part_1#install_locate_tool)
- [Install ‘postfix’ and related mail tools.](ubuntu_server_usage_part_1#install_postfix_and_mail_tools)
- [Install and enable ‘sysstat.’](ubuntu_server_usage_part_1#install_sysstat)
- [Fix for slow SSH client connections.](ubuntu_server_usage_part_1#fix_slow_ssh_connections)
- [Adjust the MOTD (Message of the Day) header and related info.](ubuntu_server_usage_part_1#adjust_motd)


### <a name="aptitude"></a> Be sure to install ‘aptitude’ first.

Before anything else, let’s make sure that `aptitude` is installed to manage package installs in Ubuntu. I know some folks prefer `apt-get`, but I find `aptitude` to be nicer and easier to use. It appears to be installed by default with Ubuntu 12.04, but just in case it isn’t do the following to install `aptitude`:

    sudo apt-get update
    sudo apt-get install aptitude

### <a name="disable_root"></a> Disable the ‘root’ account.

Now, depending on how your initial install of Ubuntu occurred, you were either prompted to create an initial user with administrator rights or you were assigned a user. I’m not going to run down the list how an initial user might have been generated, but there is one overarching philosophy here: If your administrative user is named `root` that is not good from a security standpoint.

There are are tons of hacks and exploits that explicitly look for—and act on—the `root` account. So you should not use the `root` account directly for any reason and instead handle administrative functions via another user who is assigned administrative rights via `sudo`.

### <a name="create_users"></a> Create the users.

Okay, so here is where you will create a new administrative user to use in lieu of the `root` account. The name of the account can be anything you like as long as it is not something common and predictable like `admin` or `administrator`. In my case, I like to use the name `sysop` which is short for “system operator” which is a throwback to the old BBS days and works well for a case like this. Just add the new user using `adduser` like so:

    sudo adduser sysop

In addition to an administrative user, I also like to add a standard, non-administrator user to the system as well for configuration testing like so:

    sudo adduser user

For both users, be sure to choose a solid password of some sort. Something easy for you to remember but not easy for someone else to guess.

### <a name="add_sysop_to_sudoers"></a> Add the new sysop user to the ‘sudoers’ file.

So now that you have designated a new user to be an administrator, you have to actually add that user to the `sudoers` file so they can be granted administrator rights via `sudo`. Open the file like this:

    sudo nano /etc/sudoers

Look for the section that controls user privileges named “User privilege specification” which should look like this:

    # User privilege specification
    root   ALL=(ALL:ALL) ALL

Now comment out the line with the `root` user, and add another line for the newly created `sysop` like so:

    # User privilege specification
    # root   ALL=(ALL:ALL) ALL
    sysop ALL=(ALL:ALL) ALL

Once you have that set, just go ahead and lock the `root` user’s account via `passwd -l` like so:

    sudo passwd -l root

And you should now be all set to use `sysop` as your new administrative user with rights granted via `sudo`.

### <a name="create_wwwreadwrite"></a> Create the ‘www-readwrite’ group.

Since I do a lot of collaborative web development and systems administration on Ubuntu servers, I like to create a unique group which I can assign users to. This allows me to retain system administrator’s privileges while allowing other non-administrator users to have access to web-specific areas and resources without having to grant them excessive user rights.

The first step is to create the `www-readwrite` group using `groupadd`:

    sudo groupadd www-readwrite

Then add the user’s you created previously in this tutorial to the newly created `www-readwrite` group:

    sudo adduser sysop www-readwrite
    sudo adduser user www-readwrite

And finally set the user’s default group to `www-readwrite` so every file they create is actually assigned the group of `www-readwrite` like so:

    sudo usermod -g www-readwrite sysop
    sudo usermod -g www-readwrite user

With that done, your new users should be properly added to the newly created `www-readwrite` group. But to actually allow for universal group writability on the system, you need to do one more thing: adjust the default system `umask` to allow for group writability of files to begin with.

### <a name="adjust_default_umask"></a> Adjust default ‘umask’ for group writability.

By default, Unix systems are set to only allow users to write to files they themselves have created by using a system-wide `umask` (aka: user mask) of `022`. But since I like to setup servers which allow users to collaborate when connected to a common group—such as `www-readwrite`—I like to set the system-wide `umask` to `002`.

So the first step in doing this is to open up the `login.defs` file for editing like so:

    sudo nano /etc/login.defs

Look for the line with the `UMASK` setting that looks like this:

    UMASK           002

And change it to this:

    # UMASK         022
    UMASK           002

Next, you need to adjust the pam.d `umask` setting in `common-session` by opening the that file for editing like so:

    sudo nano /etc/pam.d/common-session

Then finding the line that looks like this

    session optional                        pam_umask.so

Change it like so. Note the addition of `umask=0002` at the end of the line:

    session optional                        pam_umask.so    umask=0002

Now with those two settings adjusted, any user who logs in will be creating files that are group writable. You can test this out with your current user by logging out—and logging back in—and creating a dummy test file using `touch` like so:

    touch test_file.txt

Then do a directory listing on that file like so:

    ls -la test_file.txt

And the file permissions for `test_file.txt` should look like this:

    -rw-rw-r-- 1 sysop www-readwrite 0 Mar 20 05:36 test_file.txt

Note the `rw` for the group permissions which indicates the file can be read from and written to. Which all means that your system’s default `umask` settings have been properly adjusted to allow for group writability.

### <a name="set_time_and_timezone"></a> Set the server time and related timezone info.

Okay, with that done you should set your server’s internal clock with data pulled from a remote time server by running `ntpdate` like so:

    sudo ntpdate ntp.ubuntu.com

Then you should ensure your system’s timezone is set correctly by configuring `tzdata` like so:

    sudo dpkg-reconfigure tzdata

Now let’s set up a cron job to sync with the remote NTP server on a daily basis. You want to do this to ensure the time is correct and not affected by accidental drifting caused by system reboots or other factors. First, create the `ntpdate` file like so:

    sudo nano /etc/cron.daily/ntpdate

Then add this line to that file:

    ntpdate -s ntp.ubuntu.com

Now make sure the file has executable permissions:

    sudo chmod 755 /etc/cron.daily/ntpdate

And that should all be set. But that said, as of 2012 `ntupdate` is slowly being depreciated. So while the above—which is the traditional way of syncing to an ntp time server—works as expected, the new preferred way is to sync with an ntp server is to use `ntpd` daemon which can be installed as follows:

    sudo aptitude install ntp

While you don’t need to adjust anything for daily use, the `ntpd` options can be edited here if needed:

    sudo nano /etc/ntp.conf

When that is done, your server’s ntp time server synchronization and related time zone settings should be all set.

### <a name="locale_settings"></a> Ensure that the ‘locale’ settings are correct.

The settings for `locale` help your system determine what language, country, units of measurement and other sundry localization specific items should be used by your system.  By default, your `locale` settings should have been properly set during your initial Ubuntu install. But just in case that didn’t happen—or something went wrong—run `update-locale` as follows:

    sudo update-locale

If that runs successfully, it should just bring you back to the command prompt. To check the `locale` settings, just run the command bare like this:

    locale

The output should be something like this:

    LANG=en_US.UTF-8
    LANGUAGE=
    LC_CTYPE="en_US.UTF-8"
    LC_NUMERIC="en_US.UTF-8"
    LC_TIME="en_US.UTF-8"
    LC_COLLATE="en_US.UTF-8"
    LC_MONETARY="en_US.UTF-8"
    LC_MESSAGES="en_US.UTF-8"
    LC_PAPER="en_US.UTF-8"
    LC_NAME="en_US.UTF-8"
    LC_ADDRESS="en_US.UTF-8"
    LC_TELEPHONE="en_US.UTF-8"
    LC_MEASUREMENT="en_US.UTF-8"
    LC_IDENTIFICATION="en_US.UTF-8"
    LC_ALL=

If your output doesn’t look something like that or you are getting errors about locale specific information missing, you might have to run `locale-gen` to regenerate the locale values:

    sudo locale-gen 

The run `dpkg-reconfigure` to reconfigure the locales data:

    sudo dpkg-reconfigure locales

Finally, if none of that works, you can edit the `/etc/default/locale` manually:

    sudo nano /etc/default/locale

And just set the default `locale` setting for `LANG` by cutting and pasting the following into that file:

    LANG="en_US.UTF-8"

And of course, if `en_US.UTF-8` is not your locale please be sure to change that to match your actual locale setting.

### <a name="enable_partner_pacakages"></a> Edit the ‘sources.list’ to enable partner package updates.

While Ubuntu open source community has tons of great software tools, there are tons of other great software tools available that might not be fully open source yet available for free via Canonical’s `partner` repository. So we want to enable access to that repository—so we have direct access—via `aptitude`—to install those tools. First, open the `sources.list` file so it can be edited like so:

    sudo nano /etc/apt/sources.list

Find the following chunk of configuration code:

    ## Uncomment the following two lines to add software from Canonical's
    ## 'partner' repository.
    ## This software is not part of Ubuntu, but is offered by Canonical and the
    ## respective vendors as a service to Ubuntu users.
    # deb http://archive.canonical.com/ubuntu/ precise partner
    # deb-src http://archive.canonical.com/ubuntu/ precise partner

And uncomment the last two lines of that to enable access to the `partner` repository:

    deb http://archive.canonical.com/ubuntu/ precise partner
    deb-src http://archive.canonical.com/ubuntu/ precise partner

Now with that done, let’s update `aptitude` so it can grab the `partner` sources list:

    sudo aptitude update

With that done, your local `aptitude` setup should now be set to access and install software packages from Canonical’s `partner` repository.

### <a name="install_base_level_tools"></a> Install base level tools and utilities.

At this point you should have a somewhat functional Ubuntu server in place with the bare minimum items needed to do some basic things. But we’re not done yet.

There are lots of other useful software tools that can be installed to make your life easier. And you can install them via this `aptitude install` command:

    sudo aptitude install dnsutils traceroute nmap bc htop finger curl whois rsync lsof iftop figlet lynx mtr-tiny iperf nload zip unzip attr sshpass dkms mc elinks ntp dos2unix p7zip-full nfs-common imagemagick slurm sharutils uuid-runtime chkconfig quota pv trickle

While there are literally tons of varied software tools available from the Ubuntu community, the items listed above are the rock-solid core of the command line system administration tools I rely on on a daily basis. 

I’m not going to do a tool-by-tool breakdown of usage in this tutorial, but if you already know anything about how Unix systems work, you can pretty much understand how these tools are used by just looking at their names. That said if you don’t know what these tools are, I encourage you to read up on these tools by using the `man` command from the command line like so:

    man htop

That will give you the manual page (aka: `man` page) for `htop`; an excellent open source replacement for the commonly used system tool `top`.

### <a name="install_development_tools"></a> Install compilers, GIT, SVN and general development related stuff.

If you are going to use your Ubuntu server for software development on any level, you need to install some basic tools to get the job done like so:

    sudo aptitude install build-essential git git-core subversion git-svn

That command will install the whole suite of Debian compilers such as `gcc`/`g++` as well as SCM (source code management) tools such as `git`, `svn` (Subversion) and related utilities like `git-svn` which you can use to bridge and convert `svn` repositories to `git` repositories.

### <a name="install_locate_tool"></a> Install and initialize the ‘locate’ tool.

Now we’ll install a tool that will make your life lots easier: `locate`. Simply put, if you know what `Spotlight` is in Mac OS X, then you know what `locate` is. It’s a tool that indexes content on your file system and allows you to quickly find files—including full file paths—with ease.

Your first step in using it is to actually install it like so:

    sudo aptitude install locate

Next, run the `updatedb` command so the `locate` command actually has working database to use:

    sudo updatedb

Now do a search for a a file—such as the `test_file.txt` we created above—like so:

    locate test_file.txt

The result returned should be:

    /home/sysop/test_file.txt

Being able to get to content quickly and easily via the command line is invaluable. And `locate` will help make your life working in the command line a bit easier.

### <a name="install_postfix_and_mail_tools"></a> Install ‘postfix’ and related mail tools.

Most any Unix server should have basic mail transfer agent (MTA) capabilities. And that is where `postfix` and `mailutils` come into play.

The software tool `postfix` is a great mail transfer agent that’s easy to use, quite powerful and is the one of the most commonly used Unix mail transfer agents out there. The items that comprise the core of `mailutils` are some simple and useful tools that can make managing your server’s mail subsystem a bit easier. To install them, run the following `aptitude` command:

    sudo aptitude install postfix mailutils

Once installed, open up the `postfix` config file:

    sudo nano /etc/postfix/main.cf

And change these values to to match your server settings:

    myhostname = sandbox.local
    mydestination = sandbox.local, localhost.localdomain, localhost

Using my example above, the main thing to change is the value of `sandbox.local` which should match whatever the actual hostname of your machine is.

### <a name="install_sysstat"></a> Install and enable ‘sysstat.’

One great tool to use to collect, report, or save system activity information is `sysstat`; sometimes referred to as `sar` since that is the actual command used to read the data `sysstat` collects. You can install `sysstat` via `aptitude` like this:

    sudo aptitude install sysstat

Now open the `sysstat` config file like so:

    sudo nano /etc/default/sysstat

And set the `ENABLED` value to `true`:

    ENABLED="true"

Once that is done, restart the `sysstat` service:

    sudo service sysstat restart

Now wait about 10-15 minutes to a half hour to allow `sysstat` to actually collect data and run the the `sar` command like so:

    sar -q

The results should be something like this:

    09:55:01 PM   runq-sz  plist-sz   ldavg-1   ldavg-5  ldavg-15   blocked
    10:05:01 PM         2       130      1.18      1.48      1.05         0
    10:15:02 PM         1       126      0.01      0.80      1.11         0
    10:25:01 PM         1       126      0.01      0.18      0.64         0
    10:35:02 PM         2       135      1.58      1.59      1.10         0
    Average:            2       127      0.45      0.83      0.95         0

That output reflects the system load average history and allows you to note when—and if—there was a spike in system activity. Very useful in diagnosing system issues after they happen.

### <a name="fix_slow_ssh_connections"></a> Fix for slow SSH client connections.

Sometimes a fresh install of Ubuntu can suffer from slow initial SSH connections when using password authentication. This happens because SSH has `password` authentication set as the last authentication option by default. So you want to edit the SSH config to push `password` authentication closer to the top of the authentication method list. First, open up the `ssh_config` for editing:

    sudo nano /etc/ssh/ssh_config

Now add the following line to the bottom of the configuration options in `ssh_config` like so:

    PreferredAuthentications publickey,password,gssapi-with-mic,hostbased,keyboard-interactive

With that done, restart the SSH daemon:

    sudo service ssh restart

When all that is done, initial SSH connections to your Ubuntu machine using password authentication should run smoothly and without any unnecessary lags or delays.

### <a name="adjust_motd"></a> Adjust the MOTD (Message of the Day) header and related info.

And now, let’s adjust the MOTD (Message of the Day) settings to streamline the initial “Welcome” screen you see when you first login. The first thing I like to do is to use `figlet` to create an ASCII-art-like banner that clearly identifies what server I just logged into. I do this because—let’s face it—sometimes things can get so frenzied you can loose sight of what machine you are logging into. Which is not a good thing. So let’s create a banner for our server—in this case named `Sandbox`—like so:

    figlet Sandbox

The output of that should be an ASCII-art-like banner that looks like this:

     ____                  _ _               
    / ___|  __ _ _ __   __| | |__   _____  __
    \___ \ / _` | '_ \ / _` | '_ \ / _ \ \/ /
     ___) | (_| | | | | (_| | |_) | (_) >  < 
    |____/ \__,_|_| |_|\__,_|_.__/ \___/_/\_\
                                         

Now let’s open up the `motd.tail` file for editing like so:

    sudo nano /etc/motd.tail

And simply copy and paste the ASCII-art-like banner that was just created into that `motd.tail`. Now log out and log back in to see that banner appear once you have successfully logged in.

Additionally, there are some other scripts that run when you login that I find to be annoying—and only get in the way—that I prefer to disable.

First, I get rid if the symbolic links to the `landscape sysinfo` and `cloudguest`:

    sudo rm /etc/update-motd.d/50-landscape-sysinfo
    sudo rm /etc/update-motd.d/51-cloudguest

Then, I comment out the content of `updates-available` as well as `release-upgrade`:

    sudo nano /etc/update-motd.d/90-updates-available
    sudo nano /etc/update-motd.d/91-release-upgrade

Technically speaking, I could conceivably just delete the `updates-available` and `release-upgrade` files as well. But I personally do not like destroying files and data that I could potentially need or use in the future. So I find that commenting out the few lines of core functionality in those two files to a preferable way of neutering them in this case.

***

*Using an Ubuntu Linux Server • Part 1: Configuring a Base Level Ubuntu Server (c) by Jack Szwergold*

*This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*
