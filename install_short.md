# Artix Linux

Headless OpenRC based Archlinux system installation guide

---

## Short Install notes
Only commands guide for advanced users 

Go to [download site](https://artixlinux.org/download.php).
Download iso starting with 'artix-base-openrc'.
   
su - 

cfdisk /dev/sda # [new], [primary], [bootable], [write], 'yes'. 

mkfs.ext4 -L SYS /dev/sda1

mount /dev/sda1 /mnt

ping artixlinux.org
        
pacman -Syy

basestrap /mnt base base-devel openrc

basestrap /mnt linux linux-firmware 
    
fstabgen -U /mnt >>/mnt/etc/fstab

nano /mnt/etc/fstab # check uuid 

artools-chroot /mnt

pacman -S vi nano grub os-prober

ln -sf /usr/share/zoneinfo/Region/City /etc/localtime

hwclock --systohc
        
locale-gen

nano /etc/locale.gen #uncomment 'en_US.UTF-8 UTF-8'

nano /etc/conf.d/keymaps #replace keymap="us" with keymap="croat", see '/usr/share/kbd/keymaps/'

nano /etc/conf.d/consolefont #set: consolefont="lat2-16", google keywords "gentoo consolefont yourCountry"

rc-update add keymaps boot

rc-update add consolefont boot

grub-install --recheck /dev/sda

grub-mkconfig -o /boot/grub/grub.cfg

passwd
        
useradd -m myusername

passwd myusername

usermod -aG wheel,audio,video,optical,storage myusername

groups myusername

visudo #uncomment '%wheel ALL=(ALL) ALL'    to exit type :wq
 
nano /etc/hostname #add line: myhost
        
nano /etc/hosts #add to file

        127.0.0.1  localhost
        ::1 localhost
        127.0.1.1  myhost.localhost myhost

pacman -S networkmanager networkmanager-openrc network-manager-applet

rc-update add NetworkManager default
     
exit

umount -R /mnt

reboot
        
           
        
---
