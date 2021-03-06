# nbd-kernel_mod
Modified &amp; extended kernel driver for Network Block Devices (nbd),
based on the mainline Linux kernel nbd driver module.


                      Network Block Device (TCP version)
                                       
   What is it: With this compiled in the kernel (or as a module), Linux
   can use a remote server as one of its block devices. So every time
   the client computer wants to read, e.g., /dev/nb0, it sends a
   request over TCP to the server, which will reply with the data read.
   This can be used for stations with low disk space (or even diskless -
   if you boot from floppy) to borrow disk space from another computer.
   Unlike NFS, it is possible to put any filesystem on it, etc. It should
   even be possible to use NBD as a root filesystem (I've never tried),
   but it requires a user-level program to be in the initrd to start.
   It also allows you to run block-device in user land (making server
   and client physically the same computer, communicating using loopback).
   
   Current state: It currently works. Network block device is stable.
   I originally thought that it was impossible to swap over TCP. It
   turned out not to be true - swapping over TCP now works and seems
   to be deadlock-free, but it requires heavy patches into Linux's
   network layer.
   
   For more information, or to download the nbd-client and nbd-server
   tools, go to http://nbd.sf.net/.

   Howto: To setup nbd, you can simply do the following:

   First, serve a device or file from a remote server:

   nbd-server <port-number> <device-or-file-to-serve-to-client>

   e.g.,
	root@server1 # nbd-server 1234 /dev/sdb1

	(serves sdb1 partition on TCP port 1234)

   Then, on the local (client) system:

   nbd-client <server-name-or-IP> <server-port-number> /dev/nb[0-n]

   e.g.,
	root@client1 # nbd-client server1 1234 /dev/nb0

	(creates the nb0 device on client1)

   The nbd kernel module need only be installed on the client
   system, as the nbd-server is completely in userspace. In fact,
   the nbd-server has been successfully ported to other operating
   systems, including Windows.
