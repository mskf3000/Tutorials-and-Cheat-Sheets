## Different Ways of Wiping Disk Space

By Jack Szwergold

Zero out free space on a volume like this:

    cat /dev/zero >> /Volumes/Untitled/junk &

Write out random junk to the free space on a volume like this:

    cat /dev/urandom >> /Volumes/Untitled/junk_random &

***

*Different Ways of Wiping Disk Space (c) by Jack Szwergold; written October 8, 2015. This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*