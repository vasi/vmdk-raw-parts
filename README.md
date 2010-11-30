vmdk-raw-parts
==============

**Create virtual disks which expose physical partitions**

Why
---

When you dual-boot your computer, sometimes you want to switch into another OS for just a moment. Rather than having to reboot, you could just load your other OS as a virtual machine. To do this, you need a virtual disk that gives the VM access to the physical partition(s) on which your other OS lives. Current tools make unduly difficult, so this is my attempt to remedy the situation.

Features
--------

* Produces VMDK files compatible with VMWare and VirtualBox products, on both Mac and Linux hosts
* Partitioning schemes supported: MBR, GPT, and hybrid GPT
* Can expose multiple partitions in one virtual disk
* Can set bootable flag on a virtual partition
* Makes it easy to regenerate virtual disk when the partitioning scheme changes
* When run with sudo, produces virtual disks owned by the user

Usage
-----

`vmdk-raw-parts [options] DEVICE OUTPUT [PART1 ...]`

`DEVICE` is the physical disk whose partitions should be exposed. It should be a partitioned device, not an individual partitions: specify /dev/sda or /dev/disk0, *not* /dev/sda1 or /dev/disk0s1.

The `OUTPUT` will be a directory containing a VMDK file, as well as one or more auxiliary files. Do not remove the auxiliary files or the virtual disk will no longer work! Also in the output directory is a 'regen.sh' file, which you can run to regenerate the virtual disk if the partition table ever changes.

The partitions to expose (`PART1`...) may be specified as integers or as names. Partitions are numbered starting with '1'. Names only work with GPT partitions, MBR partitions are unnamed.

Supported options are:

* `-b, --bootable=PART`

	Mark the specified partition as bootable in the MBR. Obviously, this partition must actually be present in the MBR. The default is to leave the MBR as it is.

* `-p, --platform=PLATFORM`

	Create VMDK files in a style appropriate for the given platform, either 'darwin' or 'linux'. The default is the current platform, or 'linux' if unknown. Note that if you're exposing partitions of an image file, rather than an actual physical partition, you'll probably want linux-style output even if you're on a Mac.

Limitations
-----------

Patches welcome!

* Generally under-tested, demons may fly out of your nose!
* Logical/extended MBR partitions are not supported.
* The GPT checksum is not examined, so garbage GPT partitioning will yield a garbage VMDK.
* The resulting VMDKs will not work under Windows, though it should be faily easy to modify them for that purpose.
* In common with other solutions, things are likely to break when you change how your disk is partitioned, even if all the exposed partitions remain the same. But you can run the 'regen.sh' command inside the virtual disk to regenerate it easily.

Alternatives
------------

* VMWare and Parallels products for the Mac will automagically make Windows "Boot Camp" partitions available in a VM.
* VMWare Fusion includes a 'vmware-rawDiskCreator' program. It only works with MBR partitions, though it can understand partitions that are in the MBR part of a hybrid GPT. It also can only expose one partition in the disk.
* VirtualBox includes a hidden command 'VBoxManage internalcommands createrawvmdk'. It also only understands MBR partitions.
