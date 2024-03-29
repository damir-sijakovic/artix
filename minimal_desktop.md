# Artix Linux - Minimal Desktop

Archlinux with OpenRC guide 

---

## Intro

Basically here we will install DE (desktop environment) that is visually
close to Windows7 but more configurable and simpler to use. 

## Install packages

After base install and when you have booted into new system, run:

        sudo pacman -S xorg xfce4 xfce4-goodies pamac xarchiver catfish p7zip unzip pulseaudio firefox telegram-desktop ttf-opensans ttf-liberation ttf-font-awesome
        
After installation run:

        startxfce4

## Updating desktop theme

To get themes go to: [xfce-look](https://www.xfce-look.org). In your home folder
create .themes (ctrl+h to show hidden files) and unpack your theme archive inside. 
To add fonts create .fonts folder and drop font files inside.
Go to Settings->Appearance to set theme and font. Change font
to 'Noto Sans' (Firefox install), 'liberation mono' for terminal.
Normal OS typography needs are covered with Noto, Liberation, Open Sans and Source Code Pro. 


## Local Keyboard

To setup keymaps, go to menu Settings->Keyboard->Layout.
Click edit and choose your locale keyboard layout.

## Graphics card driver
To get info about hardware install:

        pacman -S lshw

Find your graphics card: 

        lshw -short | grep -i --color display

Google:
        yourGfxCard archlinux install

## Get help online

Run telegram-desktop and go to https://t.me/artixlinux group.

## Get back to terminal (exit Xorg)

Simply Logout.
        
---
![alt text](https://raw.githubusercontent.com/damir-sijakovic/artix/master/files/xfce.png)

XFCE desktop
