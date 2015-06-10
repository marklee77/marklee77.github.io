---
layout: post
title: "Creating NFS-root VMWare Virtual Machines from the Linux Command Line"
date: 2013-06-27 20:24
comments: true
categories: 
  - virtualmachines
  - vmware
  - linux
  - nfsroot
---

In our department there's a course that includes lab sections on programming
fluid dynamics simulations in [OpenFOAM][1], which isn't supported by the
University's IT services. We've traditionally gotten around this by installing
the required environment in a virtual machine image and running it with
[VMWare][2] under MS Windows, but there are infrastructural headaches associated
with this approach: the image is several gigabytes in size, and so distributing
to all of the machines and importing it into VMWare manually on a large number
of machines is time consuming and tedious. The research fellow who runs these
lab sections asked me to help him come up with a solution to this problem, so my
suggestion was to create some minimal virtual machine images that users could
download and import into VMware quickly, and have the root file system shared
out from a server over NFS. I've decided to document the process of creating
these images here, on the off chance it might turn out to be useful to someone
else. The commands documented below were run on an [Ubuntu][3] 12.10 system. 

<!--more-->

## Step 0: Configuring an NFS-root server, and getting appropriate kernel and
initrd files to install in the VM image.

This part is somewhat beyond the scope of this tutorial. On a working NFS server
you can use debootstrap to install a system image to a directory that will be
accessible from clients, preferably with read-only access.

Some mostly Ubuntu/Debian specific notes:

1.  Don't forget to edit etc/network/interfaces in the directory that will be
    shared out to the clients if you need to use DHCP to get a DNS server or 
    want to have any ability to interact with the interface from the VM. While 
    the networking will be pre-setup by the kernel at boot time, none of the 
    information will be made available to the operating system without 
    re-querying the DHCP server.

2.  The best way to get a compatible kernel for the clients is to chroot into 
    the root of the filesystem that you want to share, and then use apt-get to 
    install the kernel there.

        $ mount -t bind /dev /path/to/client/root/dev         
        $ mount -t bind /proc /path/to/client/root/proc
        $ mount -t bind /sys /path/to/client/root/sys
        $ chroot /path/to/client/root /bin/bash

3. It can be difficult to configure the clients to properly deal with read-only 
   nfs-mounted root filesystems. One easy way to deal with the 
   problem is to use the excellent [root-ro][4] script. Within the chroot:

        $ cp root-ro /etc/initramfs-tools/scripts/init-bottom/

    Again, you want this in the configuration directory that will be used by the 
    clients, or, more to the point, update-initramfs. When you create the 
    initrd.img by running the update-initramfs command the script will be copied 
    in and run automatically at boot time, before the root filesystem is made 
    available to user-level programs. What it does is quite clever: it moves the 
    NFS-root mount point from / to /mnt/root-ro, creates a temporary (in memory) 
    filesystem at /mnt/root-rw, and then uses overlayfs or aufs to provide the 
    root. The effect is of having a fully read-write enabled root filesystem, 
    but with all of the writes going to the tmpfs. SIDE NOTE: I prefer aufs as
    it provides the most "natural" experience, but aufs is apparently on the way 
    out, to be replaced by overlayfs. One problem with this is that the mount 
    options for overlayfs don't allow you to specify that the "lower" mount is 
    read-only. This results in the odd behavior that if a file already exists on 
    the lower file system, the file can't be edited. The work-around is to copy 
    the file to a new location, "delete" it (the lower filesystem is 
    unaffected), and then move the copy back to the old location.

    Example:

        $ cp /etc/passwd /etc/passwd.new
        $ rm /etc/passwd
        $ mv /etc/passwd.new /etc/passwd

    This way when you try to edit the file, overlayfs will know that you are 
    only trying to edit the local copy.

4.  Next, edit /etc/initramfs-tools/initramfs.conf *within the chroot*. Repeat, 
    this is to make changes to the initramfs.conf file for the _client_, and you 
    really don't want to make these changes on the server. Change "MODULES=most" 
    to "MODULES=netboot" and "BOOT=local" to "BOOT=nfs". You may also want to 
    change "NFSROOT=auto" to a hard-coded value, depending on your setup.

        $ update-initramfs -k all -c

    This will cause the needed vmlinuz and initrd.img files to be generated 
    under /boot; they may have version information appended to the file names.


## Step 1: Creating the Disk Image

The first step in the procedure is to create a properly formatted file that will
serve as the virtual disk. I used the standard utilities _dd_, _parted_, and
_fdisk_ to create the file, and installed the [syslinux][5] bootloader to the
virtual disk's MBR. 

create the empty file:

    dd if=/dev/zero of=disk.img bs=1 count=0 seek=1G

add the partition table:

    parted -s disk.img mklabel msdos

create the boot partition:

    parted -s --align=none disk.img mkpart primary 0 256M

make it bootable:

    echo -e "a\n1\nw\nq" | fdisk disk.img

add syslinux mbr (syslinux is the boot loader used to boot the kernel):

    dd bs=440 conv=notrunc count=1 if=/usr/lib/syslinux/mbr.bin of=disk.img

create home partition:

    parted -s --align=none disk.img mkpart primary 256M+1 100%

## Step 2: Create Filesystems and Install Files

For this step it's important to have the kernel and initrd files you plan on
using in one directory, called "boot" in this example, and a skeleton user home
directory, called "vmuser" in this case. You also need to create a fairly basic 
syslinux.cfg file to put in the boot directory:

    PROMPT 0
    TIMEOUT 50
    DEFAULT arch

    LABEL client
    LINUX ./vmlinuz-linux
    APPEND root=/dev/nfs nfsroot=<server-ip>:<nfs-path> ip=::::::dhcp
    INITRD ./initramfs-linux.img

attach loopback device to the disk:

    sudo losetup /dev/loop0 disk.img

add partitions to /dev:

    sudo kpartx -a /dev/loop0

create filesystems:

    sudo mkfs -t ext4 /dev/mapper/loop0p1
    sudo mkfs -t ext4 /dev/mapper/loop0p2

mount filesystems:

    sudo mkdir /mnt/vmboot
    sudo mount /dev/mapper/loop0p1 /mnt/vmboot
    sudo mkdir /mnt/vmhome
    sudo mount /dev/mapper/loop0p2 /mnt/vmhome

install syslinux in boot directory:

    sudo extlinux --install /mnt/vmboot

install kernel and config:

    sudo cp boot/* /mnt/vmboot

install home:

    sudo cp -r vmuser /mnt/vmhome/

set uid/gid:

    sudo chown -R 1000:100 /mnt/vmhome/vmuser

clean up after yourself:

    sudo umount /mnt/vmboot
    sudo rmdir /mnt/vmboot

    sudo umount /mnt/vmhome
    sudo rmdir /mnt/vmhome

    sudo kpartx -d /dev/loop0

    sudo losetup -d /dev/loop0

The disk.img file is now suitable for booting! In the next section we'll tackle
how to create an ovf file for use with vmware.

## Step 3: Convert the Image to work with VMWare

For this part I first went to easyvmx.com to create a basic virtual machine
image, and then replaced the provided root disk with my own. Since that site
seems to be down these days, I'm linking to a copy the one I use [here][6].
I use the kvm-img utility to convert the disk.img to a format that is compatible
with VMware and then use [ovftool][7] to bundle the vmx into a compressed ova
archive. The ovftool utility is available as a download from VMware.

convert to vmdk:

    kvm-img convert -O vmdk disk.img disk.vmdk

add to vmx:

    mv disk.vmdk OpenFoam_Client/OpenFoam_Client.vmdk

convert to ovf:

    ovftool --compress OpenFoam_Client/OpenFoam_Client.vmx boot.ova

And that's it! If you think this is useful, or you tried to follow my 
instructions and had problems, please comment on this post.

[1]: http://openfoam.com "OpenFOAM: The open source CFD toolbox"

[2]: http://www.vmware.com "VMware"

[3]: http://ubuntu.com "Ubuntu Linux"

[4]: /files/root-ro

[5]: http://syslinux.org "The Syslinux Project"

[6]: /files/OpenFoam_Client.tar.bz2 "OpenFOAM NFS-root Client VMX"

[7]: http://communities.vmware.com/community/vmtn/automationtools/ovf "OVF Tool"
