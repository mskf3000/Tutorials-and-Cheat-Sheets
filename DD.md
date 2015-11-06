# Cheat Sheet - DD

By Jack Szwergold, September 15, 2015

#### Benchmark disk speed with DD.

Test write speed:

    time dd if=/dev/zero of=/Volumes/Untitled/testfile bs=1024 count=2048

Test read speed:

    time dd of=/dev/null if=/Volumes/Untitled/testfile bs=1024

#### Create dummy/test files filled with zeros.

Create a 10MB test file filled with zeros:

    dd if=/dev/zero of=TESTFILE_10MB bs=1024 count=0 seek=$[1024*10]

Create a 1GB test file filled with zeros:

    dd if=/dev/zero of=TESTFILE_1GB bs=1024 count=0 seek=$[1024*10*10*10]

Create a 7GB test file filled with zeros.

    dd if=/dev/zero of=TESTFILE_7GB bs=1024 count=0 seek=$[1024*10*10*10*7]

#### Create dummy/test files filled with random data.

Create a 10MB test file filled with random data:

    dd if=/dev/urandom of=TESTFILE_10MB bs=1024 count=0 seek=$[1024*10]

Create a 1GB test file filled with random data:

    dd if=/dev/urandom of=TESTFILE_1GB bs=1024 count=0 seek=$[1024*10*10*10]

Create a 7GB test file filled with random data:

    dd if=/dev/urandom of=TESTFILE_7GB bs=1024 count=0 seek=$[1024*10*10*10*7]

#### Recover a supposedly “dead” volume using DD.

If a volume is accessible—and shown as a connected device on the system—but the file system can’t be mounted, there is still some data recovery hope by using `dd`. By using a `dd` command like this, you can force `dd` to copy whatever data can be read off of the system into a new disk image:

    sudo dd bs=512 if=/dev/disk[device id]s[partition id] of=/path/to/disk/image/directory/disk_image_of_device.dmg conv=sync,noerror

The `bs=512` translates to a block size (`bs`) of `512` which should be adjusted depending on actual device block size. And the `sync`—in `conv=sync,noerror`—tells the `dd` command to pad every input block with NULs which combined with `noerror` allows `dd` to continue after any error it hits. This basically means that even if there are bad sectors/blocks on the file system, `dd` will punch through and try to recover as much data as possible.

#### Monitor DD processes that have already started.

To monitor a `dd` process, open up another Terminal session and run this command:

    sudo kill -USR1 $(pgrep ^dd)

While it uses `kill` it does not stop the process. It will display the process in the Terminal window where `dd` is running.

To have that info update every 10 seconds, run this variant of that same command using `watch`:

    watch -n10 'sudo kill -USR1 $(pgrep ^dd)'

#### Using PV (pipe viewer) to monitor DD processes.

Install the `pv` (pipe viewer) daemon:

    sudo aptitude install pv

Then reformat your `dd` commands to insert `pv` inbetween the input files (`if`) and output files (`of`) like this:

    dd if=/dev/zero | pv | dd of=/dev/null

***

*Cheat Sheet - DD (c) by Jack Szwergold*

*This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*