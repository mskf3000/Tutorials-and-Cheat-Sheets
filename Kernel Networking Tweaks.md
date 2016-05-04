## Kernel Networking Tweaks

By Jack Szwergold, October 7, 2015

The main reason for this document existing is attempting to deal with oddball network connectivity issues in a mixed Windows/Mac environment. Here is what I know and have done.

### TCP ACK settings.

First, run this command to get the system’s current settings with `sysctl`:

    sysctl net.inet.tcp.delayed_ack

The returned value should be 0, 1, 2 or 3. Here is what each of these settings mean.

- **0:** Delayed ACK Off: An ACK responds after every packet.
- **1:** Delayed ACK On: Always employs delayed ACK, 6 packets can get 1 ACK.
- **2:** Compatibility Mode: Immediate ACK after 2nd packet, 2 packets per ACK.
- **3:** Default: The system should auto detect when to employ delayed ACK, 4 packets per ACK.

#### Adjusting the `tcp.delayed_ack` for the current session.

A setting of “0” or “2” usually helps improve copying speed to Windows shares. These settings can be changed for the current session using the following `sysctl` commands. This would adjust `delayed_ack` to 0:

	sudo sysctl -w net.inet.tcp.delayed_ack=0

And this would adjust `delayed_ack` to 2:

	sudo sysctl -w net.inet.tcp.delayed_ack=2

If you want to retain these settings on reboot, edit the `sysctl.conf` file like this:

    sudo nano /etc/sysctl.conf

And set an entry like this in there; adjust the “0” to “2” if you wish:

	net.inet.tcp.delayed_ack=0

Save that file and the `delayed_ack` will persist between reboots.

### Some advanced TCP tweaks.

Check the settings with `sysctl`:

	sysctl net.inet.tcp.delayed_ack
	sysctl net.inet.tcp.mssdflt
	sysctl kern.ipc.maxsockbuf
	sysctl net.inet.tcp.sendspace
	sysctl net.inet.tcp.recvspace

Adjust the settings for the current session like this:

	sudo sysctl -w net.inet.tcp.delayed_ack=0
	sudo sysctl -w net.inet.tcp.mssdflt=1440
	sudo sysctl -w kern.ipc.maxsockbuf=500000
	sudo sysctl -w net.inet.tcp.sendspace=250000
	sudo sysctl -w net.inet.tcp.recvspace=250000

If you want to retain these settings on reboot, edit the `sysctl.conf` file like this:

	sudo nano /etc/sysctl.conf

And set entries like this in there:
	
	net.inet.tcp.delayed_ack=0
	net.inet.tcp.mssdflt=1440
	kern.ipc.maxsockbuf=500000
	net.inet.tcp.sendspace=250000
	net.inet.tcp.recvspace=250000

***

<sup>*Kernel Networking Tweaks (c) by Jack Szwergold. This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*</sup>
