## IFTop

By Jack Szwergold

### Basic IFTop usage.

You can just launch IFTop with no options and it will show network traffic on the main network interface such as `en0` (Mac OS X) or `eth0` (Linux):

    sudo iftop

Show the network traffic on a specific network interface with the `-i` flag:

    iftop -i en0

Show the network traffic on a specific network interface with the `-i` flag and filter out sundry DNS request traffic:

    sudo -i en0 iftop -f 'not port domain'

Check for port 80 (HTTP) traffic on `en0`:

    sudo iftop  -i en0 -nBP -f 'port 80'

Check for port 548 (HTTP) traffic on `en0`:

    sudo iftop  -i en0 -nBP -f 'port 548'

Check for port 445 (HTTP) traffic on `en0`:

    sudo iftop  -i en0 -nBP -f 'port 445'

***

*IFTop (c) by Jack Szwergold; written September 14, 2015. This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*