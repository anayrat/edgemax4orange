## Edgemax Setup For Orange France FTTH

> French version and support of this guide is here
 : https://lafibre.info/orange-tutoriels/en-cours-remplacer-
sa-livebox-par-un-routeur-ubiquiti-edgemax/

### Overview
***
This setup will help you setup an edgemax router for french ISP Orange with
FTTH.

Following this tutorial you'll have internet and tv working including replays
and vod. Other services like gaming, radios are also reported to work.

**Phone will not work but we have a trick if you need it (see at the end).**

### Network setup
***

I've came up with the following setup for Orange&nbsp;_place_holder;**

**

![Orange](/t5/image/serverpage/image-id/25010i86619C9E81B7169F/image-
size/original?v=mpbl-1&amp;px=-1)

Edgemax setup

* * *

&nbsp;_place_holder; This is how we gonna set up the edgemax (based on the 3
ports).

  * 2 separated LANs on ports eth0 and eth2
  * Fully functionnal LANs with DHCP
  * LANs are isolated (static routes could link them).

On the software side :

  * Simple Stupid Setup.
  * No firewall, you'll manage this :)
  * Upnp activated on both LANs
  * Debian sources setup

![Edgemax](/t5/image/serverpage/image-id/26754i7C09DF274357CE01/image-
size/original?v=mpbl-1&amp;px=-1)

&nbsp;_place_holder; &nbsp;_place_holder; Requirements

* * *

You'll need :

  * Minimal general handling of the edgemax, CLI, editing conf, etc...
  * 1.5 beta firmware (at the moment of writing beta1 is the latest)
  * Mac address of your original Livebox provided by Orange (written on the back of the Livebox)

&nbsp;_place_holder;All files used for this tutorial are available here
:&nbsp;_place_holder;https://github.com/c0mm0n/edgemax4orange/

&nbsp;_place_holder; Get your TV mac Address

* * *

You TV mac address is the same than your livebox one with "4" added to the
last byte.

So if you have a mac adress like : a1:b2:c3:d4:e5:f6

f6 + 4 = fa

Your TV mac address will be&nbsp;_place_holder;a1:b2:c3:d4:e5:fa

&nbsp;_place_holder; Edgemax Setup

* * *

&nbsp;_place_holder;

I presume you are root, by doing :

  * "sudo -i" or prefixing commands with "sudo"

Prepare and load the conf and assets.

  * Download&nbsp;_place_holder;[https://github.com/c0mm0n/edgemax4orange/blob/master/config-orange.boot](https://github.com/c0mm0n/edgemax4orange/blob/master/config-orange.boot)
  * Edit lines 44 and 45 with your Orange logins (fti/xxxxx and password)
  * Upload file on edgemax in /config folder

&nbsp;_place_holder;

  * Download&nbsp;_place_holder;[https://github.com/c0mm0n/edgemax4orange/blob/master/dhclient.conf](https://github.com/c0mm0n/edgemax4orange/blob/master/dhclient.conf)
  * Open in a text editor
  * Replace the xx:xx:xx:xx:xx:xx with the TV mac address deduced from the livebox one (the +4 one). the "1:" must stay.
  * Upload file on edgemax in /config folder

&nbsp;_place_holder;

  * Download&nbsp;_place_holder;[https://github.com/c0mm0n/edgemax4orange/blob/master/tv.sh](https://github.com/c0mm0n/edgemax4orange/blob/master/tv.sh)
  * Upload file on edgemax in /config folder

&nbsp;_place_holder;

Now you should be able to load the conf in ssh :

  * configure
  * load config-orange.boot
  * commit

If everything went ok, you can "save" otherwise "discard" and start again,
something's wrong.

Now it's time to plug ONT on port eth1, your LAN on eth0 or eth2, and reboot
the edgemax.

If everything went ok, you now have Internet access through the edgemax.

Now for the TV services

  * Install vlan : either "apt-get install vlan" (untested but should be the easiest method)
  * or get it there :&nbsp;_place_holder;[https://github.com/c0mm0n/edgemax4orange/blob/master/vlan_1.9-3_mips.deb](https://github.com/c0mm0n/edgemax4orange/blob/master/vlan_1.9-3_mips.deb)&nbsp;_place_holder;and install with "dpkg -i&nbsp;_place_holder;/config/scripts/post-config.d/vlan_1.9-3_mips.deb"

Then, in ssh :

  * modprobe 8021q

&nbsp;_place_holder;

Now is time to launch my custom script which will :

  1. Set priorities on Vlans
  2. Launch DHCP on br0
  3. Launch igmpproxy
  * sh /config/tv.sh

Now you should :

  1. Have internet access
  2. Got an IP through dhcp on br0

Check like this for the IP on br0

  * show interfaces

![interfaces](/t5/image/serverpage/image-id/26758i452ACC2D3564A8D6/image-
size/original?v=mpbl-1&amp;px=-1)

&nbsp;_place_holder;

One last thing for replays/vod, static routes.

  * Get the IP you got from the dhcp on br0
  * Let's say this IP is&nbsp;_place_holder;10.54.56.154
  * Change the last part with "254", we now have :&nbsp;_place_holder;10.54.56.254

Use this gateway to setup your static routes like this&nbsp;_place_holder;

&nbsp;_place_holder; &nbsp;_place_holder; &nbsp;_place_holder;
&nbsp;_place_holder; route add -net 80.10.117.120/31 gateway 10.54.56.254

&nbsp;_place_holder; &nbsp;_place_holder; &nbsp;_place_holder;
&nbsp;_place_holder; route add -net 81.253.206.0/24 gateway 10.54.56.254

&nbsp;_place_holder; &nbsp;_place_holder; &nbsp;_place_holder;
&nbsp;_place_holder; route add -net 81.253.210.0/23 gateway 10.54.56.254

&nbsp;_place_holder; &nbsp;_place_holder; &nbsp;_place_holder;
&nbsp;_place_holder; route add -net 81.253.214.0/23 gateway 10.54.56.254

&nbsp;_place_holder; &nbsp;_place_holder; &nbsp;_place_holder;
&nbsp;_place_holder; route add -net 172.19.20.0/23 gateway 10.54.56.254

&nbsp;_place_holder; &nbsp;_place_holder; &nbsp;_place_holder;
&nbsp;_place_holder; route add -net 172.20.224.167/32 gateway 10.54.56.254

&nbsp;_place_holder; &nbsp;_place_holder; &nbsp;_place_holder;
&nbsp;_place_holder; route add -net 172.23.12.0/22 gateway 10.54.56.254

&nbsp;_place_holder; &nbsp;_place_holder; &nbsp;_place_holder;
&nbsp;_place_holder; route add -net 193.253.67.88/29 gateway 10.54.56.254

&nbsp;_place_holder; &nbsp;_place_holder; &nbsp;_place_holder;
&nbsp;_place_holder; route add -net 193.253.153.227/32 gateway 10.54.56.254

&nbsp;_place_holder; &nbsp;_place_holder; &nbsp;_place_holder;
&nbsp;_place_holder; route add -net 193.253.153.228/32 gateway 10.54.56.254

**You must replace&nbsp;_place_holder;10.54.56.254 with the value you deduced base on the DHCP IP of br0&nbsp;_place_holder;and type this through ssh on the edgemax.**

**Now reboot your Orange TV Box, everything should work fine.**

**If everything is OK :**

**Copy / Move the "tv.sh" script in&nbsp;_place_holder;/config/scripts/post-config.d/ for automatic startup execution.**

**You'll probably need to (unsure, check the script rights)**

  * **chmod +x tv.sh**

