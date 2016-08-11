# My Arch Linux Installation

This document describes the installation procedure I've done to install Arch Linux. The official [Installation guide]() or the [Beginners' guide ]() provide a more verbose description on most of the commands.

The installation is divided in the following sections:

* [Partitioning](#preparation)
* [Installation](#installation)
* [Configuration](#configuration)
* [Post-installation](#post-installation)

<a name="partitioning"/>
## Partitioning

Create the `root` partition:

    (parted) mkpart primary ext4 **start** **end**
    (parted) name **x** "Arch Linux"

    # mkfs.ext4 /dev/sda**x**

Create and activate the `swap` partition:

    (parted) mkpart primary linux-swap **start** **end**
    (parted) name **x** "Linux Swap"

    # mkswap /dev/sda**x**
    # swapon /dev/sda**x**

Mount the `root` partition to the `/mnt` directory:

    # mount /dev/sda**x** /mnt

Create the `/mnt/boot/efi` directory and mount the (already existing) EFI System Partition:

    # mkdir -p /mnt/boot/efi
    # mount /dev/sda**x** /mnt/boot/efi

<a name="installation"/>
## Installation

Install the recommended minimum of packages:

    # pacstrap /mnt base base-devel

Generate an `fstab` file:

    # genfstab -U /mnt >> /mnt/etc/fstab

<a name="configuration"/>
## Configuration

Before doing some configuration, run the `arch-chroot` script to change root into the new system:

    # arch-chroot /mnt /bin/bash

Uncomment `en_US.UTF-8` in `/etc/locale.gen` and generate the locale:

    # locale-gen

Create `/etc/locale.conf` and insert an uncommented entry:

    # echo LANG=en_US.UTF-8 > /etc/locale.conf

Select a time zone:

    # tzselect

Create the symbolic link `/etc/localtime`:

    # ln -s /usr/share/zoneinfo/**zone**/**sub-zone** /etc/localtime

Adjust the time skew and set the time standard to UTC:

    # hwclock --systohc --utc

> **Note:** This will make Windows display the time incorrectly, and must be configured accordingly. See [Time#UTC in Windows](https://wiki.archlinux.org/index.php/Time#UTC_in_Windows) for instructions.

Download and install the bootloader:

    # pacman -S grub efibootmgr
    # grub-mkconfig -o /boot/grub/grub.cfg
    # grub-install /dev/sda

Set the root password:

    # passwd

<a name="post-installation"/>
## Post-installation

From here you have a minimal and configured installation of Arch Linux, side-by-side with Windows. You can now exit the chroot environment by running `exit` and thereafter reboot the system. Partitions will be unmounted automatically on shutdown.

### Install Bumblebee

Since my system has an integrated and external graphics card, I installed Bumblebee. See [Bumblebee#Installation](https://wiki.archlinux.org/index.php/bumblebee#Installation) for more information.

### GRUB and Windows

By default, GRUB doesn't have an entry for Windows. You can install `os-prober` and then regenerate your GRUB configuration. This should detect other operating systems and add entries for them.