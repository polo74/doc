# How to resize a qemu virtual machine ?

It's not always easy to resize a qemu virtual machine. Here is a generic walkthrough to resize a qcow2 file and extend the size of the associated VM partition.

### Check the storage of the virtual machine

At first, we have to check the storage of our virtual machine. It will allow us to compare theses values before and after manipulations.
```
[paul@localhost ~]$ sudo fdisk -l
Disk /dev/vda: 5 GiB, 5368709120 bytes, 10485760 sectors
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


Disk /dev/zram0: 3.82 GiB, 4098883584 bytes, 1000704 sectors
Units: sectors of 1 * 4096 = 4096 bytes
Sector size (logical/physical): 4096 bytes / 4096 bytes
I/O size (minimum/optimal): 4096 bytes / 4096 bytes
```

We can see that:
- our **disk (/dev/vda)** has a size of **5Gio**
- and our **root partition** is **3.9G** heavy.

### Stop and backup your virtual machine

Operations on the qcow2 file are not possible if the machine is running, 
And because it's always better to have a checkpoint in case of failure. If you have enough space it is preferable to create a copy of the qcow2 file.

## PART I: Resize the qcow2 disk

Now you can resize the qcow2 file. For this you can use the `qemu-img` tool.

```sh
qemu-img resize linux.qcow2 +10G
```

### Reboot your virtual machine and login into

### Check the disk size and the filesystem size

Use the `fdisk` to check the disk size:
```sh
[paul@localhost ~]$ sudo fdisk -l
GPT PMBR size mismatch (10485759 != 31457279) will be corrected by write.
The backup GPT table is not on the end of the device.
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


Disk /dev/zram0: 3.82 GiB, 4098883584 bytes, 1000704 sectors
Units: sectors of 1 * 4096 = 4096 bytes
Sector size (logical/physical): 4096 bytes / 4096 bytes
I/O size (minimum/optimal): 4096 bytes / 4096 bytes
```

We can notice the the following status:
- our disk (/dev/sda) obtined his new size: 15GiB
- our root partition (/dev/sda4) already have his 3.9G size.

Now, we have to resize the partition.

## PART II: Extend the partition EXT4 partition

To extend our partition, we use the `parted` tool. If you have a GUI, you can use Gparted.

```
[paul@localhost ~]$ sudo parted /dev/vda
GNU Parted 3.6
Using /dev/vda
Welcome to GNU Parted! Type 'help' to view a list of commands.
(parted) print                                                            
Model: Virtio Block Device (virtblk)
Disk /dev/vda: 16.1GB
Sector size (logical/physical): 512B/512B
Partition Table: gpt
Disk Flags: 

Number  Start   End     Size    File system  Name      Flags
 1      1049kB  3146kB  2097kB               p.legacy  bios_grub
 2      3146kB  108MB   105MB   fat16        p.UEFI    boot, esp
 3      108MB   1157MB  1049MB  ext4         p.lxboot  bls_boot
 4      1157MB  5369MB  4212MB  btrfs        p.lxroot

(parted) resizepart 4 14GiB
Warning: Partition /dev/vda4 is being used. Are you sure you want to continue?
Yes/No? Yes                                                               
(parted) print
Model: Virtio Block Device (virtblk)
Disk /dev/vda: 16.1GB
Sector size (logical/physical): 512B/512B
Partition Table: gpt
Disk Flags: 

Number  Start   End     Size    File system  Name      Flags
 1      1049kB  3146kB  2097kB               p.legacy  bios_grub
 2      3146kB  108MB   105MB   fat16        p.UEFI    boot, esp
 3      108MB   1157MB  1049MB  ext4         p.lxboot  bls_boot
 4      1157MB  15.0GB  13.9GB  btrfs        p.lxroot

(parted) q                                                                
Information: You may need to update /etc/fstab.

[paul@localhost ~]$                                                       
```

Explanations:

1. We start parted on our disk

```[paul@localhost ~]$ sudo parted /dev/vda
GNU Parted 3.6
Using /dev/vda
Welcome to GNU Parted! Type 'help' to view a list of commands.
```

2. We show the disk informations

```
(parted) print
Model: Virtio Block Device (virtblk)
Disk /dev/vda: 16.1GB
Sector size (logical/physical): 512B/512B
Partition Table: gpt
Disk Flags: 

Number  Start   End     Size    File system  Name      Flags
 1      1049kB  3146kB  2097kB               p.legacy  bios_grub
 2      3146kB  108MB   105MB   fat16        p.UEFI    boot, esp
 3      108MB   1157MB  1049MB  ext4         p.lxboot  bls_boot
 4      1157MB  10.7GB  9581MB  btrfs        p.lxroot
```

Here, the root partition is the 4th partition (p.lxroot)

3. Resize the disk partition

```
(parted) resizepart 4 14Gio                                               
Warning: Partition /dev/vda4 is being used. Are you sure you want to continue?
Yes/No? Yes                                                               
```

4. Check if the partition is correctly resized

```
(parted) print
Model: Virtio Block Device (virtblk)
Disk /dev/vda: 16.1GB
Sector size (logical/physical): 512B/512B
Partition Table: gpt
Disk Flags: 

Number  Start   End     Size    File system  Name      Flags
 1      1049kB  3146kB  2097kB               p.legacy  bios_grub
 2      3146kB  108MB   105MB   fat16        p.UEFI    boot, esp
 3      108MB   1157MB  1049MB  ext4         p.lxboot  bls_boot
 4      1157MB  15.0GB  13.9GB  btrfs        p.lxroot
```

5. Quit parted

```
(parted) q                                                                
Information: You may need to update /etc/fstab.
```

Don't take care of the message: `You may need to update /etc/fstab`. It is a generic message written by parted while quitting. 

### Check the new partition is correctly resized

We can check with the `fdisk` util:

```
[paul@localhost ~]$ sudo fdisk -l 
Disk /dev/vda: 15 GiB, 16106127360 bytes, 31457280 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: gpt
Disk identifier: EDB052F7-644C-490E-807C-FAD7E596DB80

Device       Start      End  Sectors  Size Type
/dev/vda1     2048     6143     4096    2M BIOS boot
/dev/vda2     6144   210943   204800  100M EFI System
/dev/vda3   210944  2258943  2048000 1000M Linux extended boot
/dev/vda4  2258944 29360128 27101185 12.9G Linux root (x86-64)


Disk /dev/zram0: 3.82 GiB, 4098883584 bytes, 1000704 sectors
Units: sectors of 1 * 4096 = 4096 bytes
Sector size (logical/physical): 4096 bytes / 4096 bytes
I/O size (minimum/optimal): 4096 bytes / 4096 bytes
```

Our root partition is now 12.9G!
