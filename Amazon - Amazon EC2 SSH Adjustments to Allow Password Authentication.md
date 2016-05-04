## Amazon - Amazon EC2 SSH Adjustments to Allow Password Authentication

By Jack Szwergold, January 17, 2016

Open up the SSH daemon config like this:

    sudo nano /etc/ssh/sshd_config

Find the line for `PasswordAuthentication` which will look like this:

    # Change to no to disable tunnelled clear text passwords
    PasswordAuthentication no

Change that `PasswordAuthentication` value to `yes` like this:

    # Change to no to disable tunnelled clear text passwords
    PasswordAuthentication yes

    sudo service ssh restart

And once you have another user setup with `sudo` rights, it’s safe to get rid of that default `ubuntu` user:

    sudo deluser --remove-home ubuntu

***

<sup>*Amazon - Amazon EC2 SSH Adjustments to Allow Password Authentication(c) by Jack Szwergold. This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*</sup>