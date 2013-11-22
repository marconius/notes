File/Printer Sharing Raspberry Pi
=================================

:tags: raspberry pi
:summary: How to use a raspberry pi to share files and printers different systems over a network


Overview
--------

The problem is having to manually unplug and plug three USB devices (a USB 
drive with all my Media and a couple of printers) to connect them to one of 
three computers. One of these computers is a Raspberry Pi, so ...

How to configure a Pi to share files and printers with Ubuntu, Windows, (and a
PS3 while we are at it?



1. Installing OS
----------------

Ok so task 1 was easy, especially with NOOBS_ written onto the SD card and you
have your Raspberry Pi plugged into a monitor. Now I got a working Pi running
Raspbian_: excitement. Task two you say? HA! no problem ...



2. Mounting the USB drive
-------------------------

For step 2, I guess the key is to learn how to mount devices manually from the
shell, 'coz I don't think that sort of shiz happens automatically in the
terminal ... or is it? We'll get back to that. This too ended up being pretty
easy. As per `mount`'s man page:

    The standard form of the mount command, is `mount -t type device dir`

`dir` is the directory where you want to mount the drive to. Need to find out 
what values to put for type and device?

.. code:: bash
  
    # fdisk -l

I ended up running

.. code:: bash
  
    # mount -t vfat /dev/sda1 ~/mnt/sda1
    
not a very friendly mounting location, but we'll figure something better out
later.

That was painless ... as easy as Pi ... ha   ha   ha  ...  :|



3. Init Stuffs
--------------

.. If necessary, configure the boot sequence so that you don't actually need to
   login to the Pi in order to expose it to the network

What we need to happen at start up so that the Pi may be used to do the 
above-mentioned tasks without any manual intervention:

* start an ssh-server: DONE!! This happens by default with Raspbian

.. _pi_mount_drive:

Mount drive automatically using fstab
`````````````````````````````````````
Mount the USB drive: for this we will have to take a look at `/etc/fstab/`

The solution I found is to add a line to ```/etc/fstab/```. Let's see if it
works. First thing is to find the drive's info

.. code : shell

    # fdisk -l

Our drive's node is `/dev/sda`, and the partition we want is `/dev/sda1`. Let's
use this info to get the UUID ... haha, joke's on me! The partion has a FAT32
filesystem, which does not have a UUID! No prob, we will use the label  
```/dev/sda1```.

Second field in the fstab is the mount point ... so where should we put the
drive's filesystem? I'll use `usb-drive` in pi's home directory ... so 
`/home/pi/usb-drive` ... oh, yeah! I gotta creakt the directory.

The third field is the system type, which in our case is `vfat`

The fourth field is for options. Here you can probably do a lot of cool things,
but I am simply going to try to set the owner to the good ol' "pi" account.
This way when I connect to the machine, I will have access to this drive
automatically. the options I want to include in this field are 
`defaults, uid=1000, gid=1000`

The fifth and sixth fields are beyond my understanding at this point, so I will
set them both to 0.

So the line I am adding to fstab is:

.. code:

    /dev/sda1 /home/pi/usb-drive vfat defaults,uid=1000,gid=1000 0 0

Now we try out the new line with `mount -a` command and ... it works!

Problems with this solution:

* no UUID or unique identifier for the drive. If I could reformat my drive's
  partition using ext4 or something, that would solve this issue, but would
  that cause problems when trying to share files with a Windows machine?


.. _pi_ip:

Configuring pi's network settings
`````````````````````````````````
  
By default, the Pi connects to the network using DHCP, which is cool, but means that you can't guarantee that it will always get the same IP address. Having the same IP address would facilitate connection to the Pi a little so we will configure the ethernet interface (ETH0) manually in '/etc/network/interfaces'.

the standard lines for eth0 are

    iface eth0 inet dhcp
    
so lets set it manually instead

    iface eth0 inet static
        address 192.168.1.2
        netmask 255.255.255.0
        gateway 192.168.1.1

So now, unless you have lost control of your network, the Pi will always be found at 192.168.1.2. It is important to choose an address outside the normal range assigned by DHCP (100-150). Gateway is the address of the router that is connected to the WAN (interwebz)



4. Connecting from Ubuntu
-------------------------

The way I made the USB drive available on my Ubuntu machine is simply to use the "Connect to Server" option from the desktop's File Menu. I used SSH to connect to the IP address set in step :ref:`pi_ip`, set 'folder' to the mounting location set in step :ref:`pi_mount_drive` and the log in info is your Pi's login info. Then i bookmarked it for good measure!

Done!




5. Connecting from Windows
--------------------------

... samba ...




6. Connecting from PS3
----------------------

    sudo apt-get mediatomb 

then configured `/etc/mediatomb/config.xml`




7. Sharing printers
-------------------

TODO!




8. What's next?
---------------

* maybe freeNAS?

* make a guest or public user instead of having to know pi's login

* a power button would be nice


.. _NOOBS: http://www.raspberrypi.org/downloads
.. _Raspbian: http://www.raspbian.org/
