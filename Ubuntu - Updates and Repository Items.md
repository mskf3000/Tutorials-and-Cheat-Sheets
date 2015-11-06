# Ubuntu - Updates and Repository Items

By Jack Szwergold, September 11, 2015

***

### Clean out old Linux Kernels.

#### Get the currently active Linux kernel version.

First, be sure to reboot to get the new kernel loaded. Once rebooted, check which kernel is your currently active kernel:

    uname -r

Note that kernel version and **do not attempt to delete that kernel**; that is your currently active Linux kernel.

#### List all Linux kernel versions installed on the system.

Next, type the command below to view/list all installed Linux kernels on your system:

    dpkg --list | grep linux-image

Look at that list and note all of the Linux kernels whose version number is *lower* than your currently active Linux kernel version. Those are the Linux kernels you are going to remove.

#### Purge the old/unwanted Linux kernel versions.

Finally, run the commands below to remove the kernel you selected:

    sudo aptitude purge linux-image-x.x.x.x-generic

As a part of the process, `grub2` should be automatically run to update the GRUB bootloader available systems list. But if it somehow doesn’t run automatically, you and run it manually like this:

	sudo update-grub2

With that done, just reboot your system and you should be all done.

### Clear up `aptitude` repository list problems.

Sometimes `aptitude` repository lists—and some related files—get mucked up for no clear reason and need to be redownloaded and rebuilt. Here is some advice on what you can do to clear up some `aptitude` repository list related issues.

Before anything, *do not ever* run an `aptitude` command with `--full-resolver` unless there is no other choice since it can muck up the system in some cases. Instead, just toss the stored repository files like this:

    sudo rm -rf /var/lib/apt/lists/*

Then just run `aptitude update` to get new/clean copies of the repository lists:

    sudo aptitude update

And all should be good again.

### If you have to use `--full-resolver` use it as a part of `safe-upgrade`.

If for some reason there are still issues with your `aptitude` repository lists and you need to run `--full-resolver`, do it with the `safe-upgrade` option:

    sudo aptitude safe-upgrade --full-resolver

And then just wait for it to do it’s thing and run the standard `sudo aptitude update`/`sudo aptitude upgrade` combo and all should be fine again.

### Dealing with installation package issues.

#### Clearing up a screwed up package install/upgrade.

If a standard `sudo aptitude install` or `sudo aptitude upgrade` screws up, you might have to run this command to clear things up:

    sudo dpkg --configure -a

#### Upgrading only a specific package.

If you need to upgrade a specific package—and not any other package—run this command:

    sudo apt-get install --only-upgrade [package name]

#### Clean out unused packages.

Sometimes stray/unused packages get left behind on a system. To clean them out just run this command:

    sudo apt-get autoremove

See if anything needs to be removed and if all looks okay, just go ahead and respond yes (`Y`) and all should be good.

### Dealing with PPAs (Personal Package Archives).

Check what PPAs (Personal Package Archives) are set on the system:

    ls -la /etc/apt/sources.list.d/

Check what repositories—including PPAs—the system checks:

    apt-cache policy

#### Installing and removing a PPA.

First install `python-software-properties` like this:

    sudo aptitude install python-software-properties

Next add the PPA repository to the system like this:

    sudo add-apt-repository ppa:whatever/ppa

Remove a specific repository using `add-apt-repository` with the `--remove` flag:

    sudo add-apt-repository --remove ppa:whatever/ppa

### Pointing Ubuntu to old repositories.

You generally want to do this if you are dealing with an out of date Ubuntu install and you simply want to get the most recent packages as possible. Remember, if the system is this out of date this should be considered only a temporary fix; any setup in this state should be upgraded via a full release upgrade if/when possible:

    sudo nano /etc/apt/sources.list

Replace all instances of `archive.ubuntu.com` with `old-releases.ubuntu.com` then do this:

    sudo apt-get update

### Dealing with loss of Hardware Enablement Stack (HWE) support.

If you are using an older version of Ubuntu such as an LTS (Long Term Support) version and you get a message like this one day on login:

> Your current Hardware Enablement Stack (HWE) is going out of support
on 08/07/14.  After this date security updates for critical parts (kernel
and graphics stack) of your system will no longer be available.

The two choices you will be presented with is:

1. Upgrading the whole OS to another major version release.
2. Upgrading just the HWE to a newer version.

Option two is *always* the best option since a full OS release upgrade risks breaking a lot more than simply upgrading the HWE. So to upgrade the HWE run a command similar to this:

    sudo aptitude install linux-generic-lts-trusty linux-image-generic-lts-trusty

That command is specific to upgrading the HWE in Ubuntu 12.04 so if you see that message come up in another version of Ubuntu be sure to use the command recommended for your particular Ubuntu install.

***

*Ubuntu - Updates and Repository Items (c) by Jack Szwergold*

*This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*