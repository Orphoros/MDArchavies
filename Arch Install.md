# Arch Linux Install Guide

> This setup guide was made for a Hungarian keyboard layout.

Load keyboard:
```
Loadkeys hu
```
Update the system clock
```
timedatectl set-ntp true
timedatectl set-timezone Europe/Budapest
timedatectl status
```
Partition the disks
```
lsblk
fdisk /dev/sda
```
In the fdisk for the EFI:
```
g
n
+550M
t
1
```
In the fdisk for the swap:
```
n
+2G
t
19
```
In the fdisk for the root:
```
n
```
In the fdisk for the write:
```
w
```
Making the file system:
```
mkfs.fat -F32 /dev/sda1
mkswap /dev/sda2
swapon /dev/sda2
mkfs.ext4 /dev/sda3
```
Mount the root partition:
```
mount /dev/sda3 /mnt
```
Install base system for Arch:
```
pacstrap /mnt base linux linux-firmware
```
Generate filesystem table:
```
genfstab -U /mnt >> /mnt/etc/fstab
```
Change root:
```
arch-chroot /mnt
```
Set timezone
```
ln -sf /usr/share/zoneinfo/Europe/Budapest
```
Set hardware clock
```
hwclock --systohc
```
Install nano
```
pacman -S nano
```
Edit locale -> Uncomment the locale (Language of the computer)
```
nano /etc/locale.gen
```
Generate locale
```
locale-gen
```
Edit Keyboard
```
nano /etc/vconsole.conf
KEYMAP=hu

OR

localectl set-keymap --no-convert hu
localectl status
```
Edit host
```
nano /etc/hostname
myhostname
```
Edit IP for host
```
nano /etc/hosts
127.0.0.1	localhost
::1		    localhost
127.0.1.1	myhostname.localdomain	myhostname
```
Set password for root
```
passwd
```
Add user and password
```
useradd -m orphoros
passwd orphoros
```
Add user to groups
```
usermod -aG wheel,audio,video,optical,storage orphoros
```
Install sudo
```
pacman -S sudo
```
Edit sudo conf file (uncomment the wheel group)
```
EDITOR=nano visud
```
Install bootloader
```
pacman -S efibootmgr dosfstools os-prober mtools
```
Make EFI dir
```
mkdir /boot/EFI
```
Mount EFI
```
mount /dev/sda1 /boot/EFI
```
Install grub
```
pacman -S grub
```
Setting up the bootloader
```
grub-install --target=x86_64-efi --bootloader-id=grub_uefi --recheck
```
Make config for grub
```
grub-mkconfig -o /boot/grub/grub.cfg
```
Install network manager
```
pacman -S networkmanager
systemctl enable NetworkManager
```
Finish everything
```
exit
umount -l /mnt
shutdown now
```