# Amazon - Amazon EBS Volume Storage

By Jack Szwergold, September 23, 2015

***

#### Login and make note of your EC2 instances.

Everything here happens in the Amazon Web Services control panel. Login to your Amazon Web Services console and go to the “EC2 Management Console”.

Under *Instances* select an instance (basically a server) you wish to attach an EBS volume to and make note of the *Instance ID* as well as the *Availablity Zone*.

#### Creating an EBS volume.

Now go to *Elastic Block Store* and choose *Volumes*. The way Amazon Web Services work, even if you have never consciously created an Amazon EBS volume, one will be created for you if you create an EC2 server. This is the root/boot partition for the machine and should not be touched or removed when adding a new volume. So you might see a volume there; don’t touch or worry about it.

Now, hit the *Create Volume* button to create a new volume. You will be given a list of options. The only ones to care about are *Type*, *Size* and *Availability Zone*. Maybe you can create a nice *Snapshot ID* name to make the storage easier to find, but it’s not needed.

For this test I am creating a 4GB volume and just leaving the *Type* to *General Purpose (SSD)* but the *Availability Zone* should be set to the exact zone as the EC2 server you want to attach it to.

Now after you hit create, the Amazon control panel will be in a state of *Creating* the volume with the indicator being set to the color yellow. When it is done, it will be in an *Available* state and the indicator is set to the color blue.

#### Attaching an EBS volume.

With it now being available, you can choose it and then select the *Actions* menu. From that menu select *Attach Volume* and then choose an *Instance* to connect the volume to. And then the *Device* will be auto-set by Amazon.

The range of devices would be from `/dev/sdf` through `/dev/sdp`. And Amazon further notes:

> Note: Newer Linux kernels may rename your devices to `/dev/xvdf` through `/dev/xvdp` internally, even when the device name entered here (and shown in the details) is `/dev/sdf` through `/dev/sdp`.

Which basically means, whatever the volume is set to here might actually be different on the actual server due to differences in the way Linux handles mounts on Amazon Web Services. A minor headache, but something to be aware of.

Now, hit the *Attach* button and wait. The “in progress” spinner should be spinning next to the device. Wait for it to go back to a solid square.

#### Checking for an EBS volume.

So with all that done, if you login to the server and run `lsblk` or `cat /proc/partitions` you should now see the device attached as expected.

And in my tests the device, instead of the volume being `sdf` it’s actually `xvdf`; so it would be `/dev/xvdf` when partitioning, formating and mounting. So yes, it can be a bit confussing but just make note of it for partitioning and setup and all should be good.

***

*Amazon - Amazon EBS Volume Storage (c) by Jack Szwergold*

*This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*