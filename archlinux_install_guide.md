## Archlinux Install Guide

**Verify the boot mode is BIOS or UEFI mode;**

```
root@archiso ~ # ls /sys/firmware/efi/efivars
```

If the command show the directory, then the system is booted in UEFI mode. If the command now show the direcotry, then the system is booted in BIOS mode.

**Verify internet connectivity;**

```
root@archiso ~ # ip a
root@archiso ~ # ping -c2 archlinux.org
```

**Verity the system clock synced automatically;**

```
root@archiso ~ # timedatectl 
               Local time: Sat 2023-03-18 05:57:18 UTC
           Universal time: Sat 2023-03-18 05:57:18 UTC
                 RTC time: Sat 2023-03-18 05:57:18
                Time zone: UTC (UTC, +0000)
System clock synchronized: yes
              NTP service: active
          RTC in local TZ: no
```

**Disk partitioning;**

```
root@archiso ~ # fdisk /dev/vda  

Welcome to fdisk (util-linux 2.38.1).
Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.

Device does not contain a recognized partition table.
Created a new DOS disklabel with disk identifier 0x26ecf54e.

Command (m for help): n
Partition type
   p   primary (0 primary, 0 extended, 4 free)
   e   extended (container for logical partitions)
Select (default p): p
Partition number (1-4, default 1): 
First sector (2048-41943039, default 2048): 
Last sector, +/-sectors or +/-size{K,M,G,T,P} (2048-41943039, default 41943039): +1G

Created a new partition 1 of type 'Linux' and of size 1 GiB.

Command (m for help): n
Partition type
   p   primary (1 primary, 0 extended, 3 free)
   e   extended (container for logical partitions)
Select (default p): 

Using default response p.
Partition number (2-4, default 2): 
First sector (2099200-41943039, default 2099200): 
Last sector, +/-sectors or +/-size{K,M,G,T,P} (2099200-41943039, default 41943039): 

Created a new partition 2 of type 'Linux' and of size 19 GiB.

Command (m for help): t
Partition number (1,2, default 2): 
Hex code or alias (type L to list all): lvm

Changed type of partition 'Linux' to 'Linux LVM'.

Command (m for help): t
Partition number (1,2, default 2): 1
Hex code or alias (type L to list all): uefi

Changed type of partition 'Linux' to 'EFI (FAT-12/16/32)'.

Command (m for help): w
The partition table has been altered.
Calling ioctl() to re-read partition table.
Syncing disks.

root@archiso ~ # fdisk -l /dev/vda
Disk /dev/vda: 20 GiB, 21474836480 bytes, 41943040 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x26ecf54e

Device     Boot   Start      End  Sectors Size Id Type
/dev/vda1          2048  2099199  2097152   1G ef EFI (FAT-12/16/32)
/dev/vda2       2099200 41943039 39843840  19G 8e Linux LVM
```

**Create Physical volume;**

```
root@archiso ~ # pvcreate /dev/vda2 
```

**Create Volume group;**

```
root@archiso ~ # vgcreate VolGroup00 /dev/vda2
```

**Create Logical volumes;**

```
root@archiso ~ # lvcreate -L 2G VolGroup00 -n lv_swap
root@archiso ~ # lvcreate -l 100%FREE VolGroup00 -n lv_root
```

**Format EFI system partition FAT32 using mkfs.fat;**

```
root@archiso ~ # mkfs.fat -F32 /dev/vda1
```

**Upload the module for creating device nodes;**

```
root@archiso ~ # modprobe dm_mod
root@archiso ~ # vgscan
root@archiso ~ # vgchange -ay
```

**Format swap partition and active swap partition;**
 
```
root@archiso ~ # mkswap /dev/VolGroup00/lv_swap 
root@archiso ~ # swapon /dev/VolGroup00/lv_swap
```

**Format lv_root partition ext4 using mkfs.ext4;**

```
root@archiso ~ # mkfs.ext4 /dev/VolGroup00/lv_root 
```

**Mount the file systems;**

```
root@archiso ~ # mount /dev/VolGroup00/lv_root /mnt
root@archiso ~ # mount --mkdir /dev/vda1 /mnt/boot/efi
```

**Install the packages using pacstrap script;**

```
root@archiso ~ # pacstrap -K /mnt base base-devel linux linux-lts linux-firmware bash-completion vim networkmanager lvm2 openssh
```

**Generate fstab file;**

```
root@archiso ~ # genfstab -U /mnt >> /mnt/etc/fstab
```

**Change root into the new installed system;**

```
root@archiso ~ # arch-chroot /mnt
[root@archiso /]# 
```

**Set the time zone accordingly;**

```
[root@archiso /]# ln -sf /usr/share/zoneinfo/Asia/Kolkata /etc/localtime
```

**Set the hardware clock;**

```
[root@archiso /]# hwclock --systohc
```

**Edit /etc/locale.gen and uncomment en_US.UTF-8 UTF-8, generate locales by running "locale-gen";**

```
[root@archiso /]# vim /etc/locale.gen 
[root@archiso /]# locale-gen
Generating locales...
  en_US.UTF-8... done
Generation complete.
```

**Create /etc/locale.conf file, set LANG veriable;**

```
[root@archiso /]# echo LANG=en_US.UTF-8 > /etc/locale.conf
[root@archiso /]# export LANG=en_US.UTF-8
```

**Set hostname;**

```
[root@archiso /]# echo archlinux.example.com > /etc/hostname
```

**Update /etc/hosts file;**

```
# Static table lookup for hostnames.
# See hosts(5) for details.
127.0.0.1	localhost.localdomain	localhost
::1		localhost.localdomain	localhost
127.0.1.1	archlinux.example.com	archlinux
```

**Edit /etc/mkinitcpio.conf the file and insert lvm2 between block and filesystems;**

```
[root@archiso /]# vim /etc/mkinitcpio.conf

HOOKS=(base udev autodetect modconf kms keyboard keymap consolefont block lvm2 filesystems fsck)
```

**Regenerate the initramfs;**

```
[root@archiso /]# mkinitcpio -p linux linux-lts
```

**Install grub packages;**

```
[root@archiso /]# pacman -S grub efibootmgr os-prober
```

**Install grub;**

```
[root@archiso /]# grub-install --target=x86_64-efi --efi-directory=/boot/efi --bootloader-id=GRUB 
Installing for x86_64-efi platform.
Installation finished. No error reported.
```

**Generate grub.cfg file;**

```
[root@archiso /]# grub-mkconfig -o /boot/grub/grub.cfg
```

**Set root password;**

```
[root@archiso /]# passwd 
New password: 
Retype new password: 
passwd: password updated successfully
```

**Create user and set password;**

```
[root@archiso /]# useradd -mG wheel arch
[root@archiso /]# passwd arch
New password: 
Retype new password: 
passwd: password updated successfully
```

**Uncomment %wheel ALL=(ALL:ALL) ALL in sudoers file to allow group wheel execute any command**

```
[root@archiso /]# EDITOR=vim visudo
wheel ALL=(ALL:ALL) ALL
```

**Enable NetworkManager and ssh services;**

```
[root@archiso /]# systemctl enable NetworkManager.service
[root@archiso /]# systemctl enable sshd.service
```

**Exit the chroot environment by typing "exit" or pressing "Ctrl+d" and restart the machine by tying "reboot";**
