## Ubuntu - Setting Up DD_Rescue Items

By Jack Szwergold

### Installation.

Install DD_Rescue:

    sudo aptitude install dd_rescue

### Example usage.

Creating an image with DD_Rescue:

    dd_rescue /dev/sdb1 image_of_sdb1.img

Copy an image with DD_Rescue via standard output to a remote server via a pipe; very useful for remote recovery:

    dd_rescue /dev/sdb1 - | ssh email_address@example.com "cat - > /users/email_address/image_of_sdb1.img"

Compress a disk image in real-time:

    dd_rescue /dev/sdb1 - | gzip > ~/image_of_sdb1.img.gzip

***

*Ubuntu - Setting Up DD_Rescue Items (c) by Jack Szwergold; written on October 5, 2015. This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*