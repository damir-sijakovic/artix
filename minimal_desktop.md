# Artix Linux - Minimal Desktop

(requires base install) Archlinux with OpenRC guide 

---

## Intro

Basically here we will install DE (desktop environment) that is visually
close to Windows7 but more configurable and simpler to use. 

## Install packages

After base install you have rebooted into new system. Then run:

        sudo pacman -S xfce4 xfce4-goodies pamac firefox    
        
After installation run:

        startxfce4

## Updating desktop theme

To get themes go to: [xfce-look](https://www.xfce-look.org). In your home folder
create .themes (ctrl+h to show hidden files) and unpack your theme archive inside. 
To add fonts create .fonts folder and drop font files inside.
Go to Settings->Appearance to set theme and font.


## Local issues

To setup keymaps, go to menu Settings->Keyboard->Layout.
Click edit and choose your locale keyboard layout.

## Graphics card driver
To get info about hardware install:

        pacman -S lshw

Find your graphics card: 

        lshw -short | grep -i --color display

Google:
        yourGfxCard archlinux install



## Get back to terminal

Simply Logout.
        
---