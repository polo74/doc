# How to resize a qcow2 file ?

It is not always easy to resize a qemu partition. Here is a generic walkthrough to resize a qcow2 file and the virtual machine partition.

## Stop the virtual machine

Operations on the qcow2 file are not possible if the machine is running, 

## Create a backup of your qcow2 file

It is always better to have a checkpoint in case of failure. 
If you have enough space it is preferable to create a copy of the qcow2 file,

## Resize the qcow2 disk

Now you can resize the qcow2 file. For this you can use the `qemu-img` tool.

```sh
qemu-img resize openvas.qcow2 +10G
```

## Reboot your virtual machine

## Check the disk size and the filesystem size

Use the `fdisk` to check the disk size:
```sh
[paul@localhost ~]$ sudo fdisk -l
Disk /dev/vda: 15 GiB, 16106127360 bytes, 31457280 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: gpt
Disk identifier: EDB052F7-644C-490E-807C-FAD7E596DB80

Device       Start      End Sectors  Size Type
/dev/vda1     2048     6143    4096    2M BIOS boot
/dev/vda2     6144   210943  204800  100M EFI System
/dev/vda3   210944  2258943 2048000 1000M Linux extended boot
/dev/vda4  2258944 10485726 8226783  3.9G Linux root (x86-64)


Disk /dev/zram0: 7.75 GiB, 8317304832 bytes, 2030592 sectors
Units: sectors of 1 * 4096 = 4096 bytes
Sector size (logical/physical): 4096 bytes / 4096 bytes
I/O size (minimum/optimal): 4096 bytes / 4096 bytes
```

Teh partition may be the same than the first check because we didn't already extend it
The disk must be resized with the new size.
