# Artix Linux

Headless OpenRC based Archlinux system installation guide

---

## Preparation
Before install on real hardware you should practice on VM like VirtualBox/Qemu or
old laptop and write down installation notes.
If you are not familiar with virtual machines you should 
first go watch tutorials about VirtualBox (on YouTube) before you do this guide. 


## Download ISO
Go to [download site](https://artixlinux.org/download.php).

Download iso starting with 'artix-base-openrc'.


## Install

In VirtualBox select default ArchLinux 64 preset.

### Boot from ISO

Default user/pass is artix/artix. So login as artix. and become super user:
    
        su - 

### Prepare partiton
Run cfdisk tool to prepare partition.

        cfdisk /dev/sda 
        
Select type DOS, make one partition with default options. After that select
[new], [primary], [bootable] and [write]. Don't forget to type 'yes'. 

Use mkfs.ext4 tool to format partition:

        mkfs.ext4 -L SYS /dev/sda1

Mount new drive to /mnt:

        mount /dev/sda1 /mnt

## Test internet connection

        ping artixlinux.org
        
## Install base    
Install kernel and base system components.

        pacman -Syy
        basestrap /mnt base base-devel openrc
        basestrap /mnt linux linux-firmware 
    
## fstab
Generate and check the fstab file.
Tell system about your harddrive.
    
        fstabgen -U /mnt >>/mnt/etc/fstab
        nano /mnt/etc/fstab 

## chroot 
Log into new system:

        artools-chroot /mnt



## Install grub and text editors
Grub is bootloader while vi and nano are text editors.
 
        pacman -S vi nano grub os-prober


## Locale

Set time zone. For example my locale is '/usr/share/zoneinfo/Europe/Zagreb'

        ln -sf /usr/share/zoneinfo/Region/City /etc/localtime
        hwclock --systohc
        
Generate locales, uncomment 'en_US.UTF-8 UTF-8' in '/etc/locale.gen':

        locale-gen
        nano /etc/locale.gen 

For more local specific settings, find your local keymap in /usr/share/kbd,
For example '/usr/share/kbd/keymaps/i386/qwertz/croat.map.gz' is croatian keymap
and the 'croat' part of filename is what you will add to '/etc/conf.d/keymaps' file
where you will replace keymap="us" with keymap="croat".

        nano /etc/conf.d/keymaps 

Set font with locale (Croatian) char symbols, set: consolefont="lat2-16".
To learn what font you should use read https://wiki.gentoo.org/wiki/Fonts and
google these keywords "gentoo consolefont yourCountry".

        nano /etc/conf.d/consolefont

And finally make changes permanent:

        rc-update add keymaps boot
        rc-update add consolefont boot


## Bootloader

Setup and install bootleader:

        grub-install --recheck /dev/sda
        grub-mkconfig -o /boot/grub/grub.cfg

## Users

Setup user and superuser.

Set superuser (root user) password:

        passwd
        
Add default user, set pass and add to groups:

        useradd -m myusername
        passwd myusername
        usermod -aG wheel,audio,video,optical,storage myusername

To test if user was added in groups:

        groups myusername

Use 'visudo' to enable 'sudo' command for user uncomment '%wheel ALL=(ALL) ALL':

        visudo        
 
To exit and save visudo type:

        :wq  

## Network

Add myhost to /etc/hostname:

        nano /etc/hostname
        myhost
        
Add to /etc/hosts:

        127.0.0.1  localhost
        ::1 localhost
        127.0.1.1  myhost.localhost myhost

Install NetworkManager: 
   
        pacman -S networkmanager networkmanager-openrc network-manager-applet
        rc-update add NetworkManager default

Setup firewall:

        nano /etc/iptables/drop.rules
        
Add these lines to file:

        # drop (block all traffic)
        *filter
        :INPUT DROP [0:0]
        :FORWARD DROP [0:0]
        :OUTPUT DROP [0:0]
        COMMIT
    
Enable firewall on startup, create startup script:

        sudo touch /etc/local.d/iptables.start
        sudo chmod +x /etc/local.d/iptables.start

Add commands to script (Note: these are VM test firewall rules, you can find better rules online):

        /sbin/iptables-restore < /etc/iptables/simple_firewall.rules
        /sbin/ip6tables-restore < /etc/iptables/drop.rules

Test rules after reboot:

        sudo iptables -L
        sudo ip6tables -L
        
##Reboot
  
After reboot remove iso from VirtualBox.
        
        exit
        umount -R /mnt
        reboot
        
##Post-installation tests

        uname -a
        ping google.com
    
            
        
---
