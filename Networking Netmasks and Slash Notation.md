## Networking Netmasks and Slash Notation

By Jack Szwergold, October 9, 2015

### What’s a netmask?

On a most basic level, a netmask is a 32-bit “mask” that is used to divide IP addresses into subnets for management, isolation and security reasons.

For example, let’s pretend we are creating a simple IP address range. It would be all addresses from `192.168.56.1` to `192.168.56.254`. The netmask for that range would be `255.255.255.0` and that’s that.

But not so fast: Not all pieces of software, devices or items would be able to use a netmask assignment format like `255.255.255.0`. Those items would need to have a “slash” notation set for the combined netmask and range.

### What’s slash notation?

#### Slash notation basics.

So using this example case, instead of `192.168.56.1` with a netmask of `255.255.255.0`, the slash notation would be `192.168.56.1/24`. That `/24` is derived from the idea that:

- An IP address has four segments.
- Each `255` equates to `11111111` bits.
- Those `11111111` bits are cumulatively considered an octet which is 8 bits.
- So three of those `255`’s are multiplied to become… `24`!

In general, when most average users use a netmask—or slash notation—it’s in terms of basic octet concepts such as:

- `255.255.255.0` = `/24`
- `255.255.0.0` = `/16`
- `255.0.0.0` = `/8`

But in some cases, slash notation can become quite intricate and complex.

#### Advanced slash notation.

For example, let’s say you are creating a subnet in which—at a maximum—16 devices can connect at any given time. The netmask and slash notation for that subnet would be:

- `255.255.255.240` = `/28`

I won’t even attempt to do the math that would correlate where that `.240` octet or the `/28` comes from. But the chart below is a good reference if you have to ever do some basic network infrastructure work and need a nice netmask/slash notation to get the job done.

	Mask value:                             # of
	Hex            CIDR   Decimal           addresses  Classfull
	80.00.00.00    /1     128.0.0.0         2048 M     128 A
	C0.00.00.00    /2     192.0.0.0         1024 M      64 A
	E0.00.00.00    /3     224.0.0.0          512 M      32 A
	F0.00.00.00    /4     240.0.0.0          256 M      16 A
	F8.00.00.00    /5     248.0.0.0          128 M       8 A
	FC.00.00.00    /6     252.0.0.0           64 M       4 A
	FE.00.00.00    /7     254.0.0.0           32 M       2 A
	FF.00.00.00    /8     255.0.0.0           16 M       1 A
	FF.80.00.00    /9     255.128.0.0          8 M     128 B
	FF.C0.00.00   /10     255.192.0.0          4 M      64 B
	FF.E0.00.00   /11     255.224.0.0          2 M      32 B
	FF.F0.00.00   /12     255.240.0.0       1024 K      16 B
	FF.F8.00.00   /13     255.248.0.0        512 K       8 B
	FF.FC.00.00   /14     255.252.0.0        256 K       4 B
	FF.FE.00.00   /15     255.254.0.0        128 K       2 B
	FF.FF.00.00   /16     255.255.0.0         64 K       1 B
	FF.FF.80.00   /17     255.255.128.0       32 K     128 C
	FF.FF.C0.00   /18     255.255.192.0       16 K      64 C
	FF.FF.E0.00   /19     255.255.224.0        8 K      32 C
	FF.FF.F0.00   /20     255.255.240.0        4 K      16 C
	FF.FF.F8.00   /21     255.255.248.0        2 K       8 C
	FF.FF.FC.00   /22     255.255.252.0        1 K       4 C
	FF.FF.FE.00   /23     255.255.254.0      512         2 C
	FF.FF.FF.00   /24     255.255.255.0      256         1 C
	FF.FF.FF.80   /25     255.255.255.128    128       1/2 C
	FF.FF.FF.C0   /26     255.255.255.192     64       1/4 C
	FF.FF.FF.E0   /27     255.255.255.224     32       1/8 C
	FF.FF.FF.F0   /28     255.255.255.240     16      1/16 C
	FF.FF.FF.F8   /29     255.255.255.248      8      1/32 C
	FF.FF.FF.FC   /30     255.255.255.252      4      1/64 C
	FF.FF.FF.FE   /31     255.255.255.254      2     1/128 C
	FF.FF.FF.FF   /32     255.255.255.255   This is a single host route

***

<sup>*Networking Netmasks and Slash Notation (c) by Jack Szwergold. This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*</sup>