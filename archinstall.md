# Arch Install 

This installation follows steps from the Arch Wiki: <https://wiki.archlinux.org/title/Installation_guide>

## VM Setup 

I first downloaded the ISO and verified with SHA1

I then created a VM in VMware Workstation using the specs below: 
- 20 GB HDD
- 2GB Ram
- Single HDD

I did run into an issue where I could only boot to the BIOS in VMware Workstation. To boot to the EFI, I used a guide and edited the `.vmx` file and added to line 2: 

```
firmware="efi"
```

Using the guide here: <>

## Booting into VM / Main Install

- Boot VM, verify UEFI
- I used the default keyboard layout

### Verifiying Boot Mode

```bash
ls /sys/firmware/efi/efivars
```

### Internet Connection

```bash
ip link
ping archlinux.org
```

### Partition Disks

```bash
fdisk -l
fdisk /dev/sda
```

I created 2 new partitions.
- `/dev/sda1` 500M - Boot partition
- `/dev/sda2` 19.5GB - Mount partition

### Formatting Partitions

I created an Ext4 file system on the mount partition `/dev/sda2` using the following command:

```bash
mkfs.ext4 /dev/sda2
```

I then formatted the EFI boot partition using the following command:

```bash
mkfs.fat -F 32 /dev/sda1
```

### Mounting the File Systems

Mounting the root volume:

```bash
mount /dev/sda2 /mnt
```
Mounting the EFI System:

```bash
mount --mkdir /dev/sda1 /mnt/boot
```

## Installation

### Essential Packages 

I installed some essential packages using the following command:

```bash
pacstrap -K /mnt base linux linux-firmware
```

## Configuring the System

### Fstab

I created an Fstab file using the command:

```bash
genfstab -U /mnt >> /mnt/etc/fstab
```

### Chroot

I then used the following command to change root into the system:

```bash
arch-chroot /mnt
```

I then installed a network package using the following command:

```bash
pacman -S networkmanager 
```

In order to be able to use the Network Manager upon startup of the system, I used the following commands: 

```bash
systemctl enable NetworkManager
systemctl start NetworkManager
```

### Time Zone

Use command: 

```bash
ln -sf /usr/share/zoneinfo/America/Chicago /etc/localtime

hwclock --systohc
```

### Localization

I generated locales using `locale-gen` and then set the default locale using: 

```bash
echo "LANG=en_US.UTF-8 LC_COLLATE=C" > /etc/locale.conf
```

### Network Config

I created the hostname file using the command: 

```bash
echo "hunterarch" > /etc/hostname
```

### Intramfs

I created intramfs using `mkinitcpio -P`

### Root Password

I set the root password using `passwd`. 
- password is hy783fk52

### Boot Loader

Installed grub using: 

```bash
pacman -S grub 
grub-mkconfig -o /boot/grub/grub.cfg
```

### Reboot

I exited the chroot by using `Ctrl+d`. 
I manually unmounted all partitions using `umount -R /mnt`. 
Finally, I restarted the machine by typing `reboot`. 

## After Reboot 

### Desktop Environment

I decided to install KDE desktop environment using the following commands and leaving all options at default.  

```bash
pacman -S xorg-server xorg-server-utils xorg-xinit
pacman -S xf86-video-vesa
pacman -S sddm
pacman -S plasma kde-applications
```

To set the DE to start upon boot I used the command `systemctl enable sddm`. 


### Accounts/Users

I created two user accounts for Hunter and Codi. 
Passwords
- Codi - GraceHopper1906
- Hunter - 67hjk39gtr

I created the users using the following commands. 

```bash
useradd -m -g users -s /bin/bash hunter
passwd hunter
useradd -m -g users -s /bin/bash codi
passwd codi 
```

### SSH

Installed ssh using the command: 

```bash
pacman -Sy ssh
```

I was able to connect to the class gateway.


