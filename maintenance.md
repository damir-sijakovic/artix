# Artix Linux - Apps & Update

Archlinux with OpenRC guide 

---


## System Update

To update system run (you don't need to be inside desktop/X):

        sudo pacman -Syu


##  To see list of all installed software run:

        pacman -Qe

## To install package localy run:

        pacman -U /root/Download/packagename.tar.gz


## Find package

        pacman -Ss searchTerm

## GUI package installer
    Pamac manager is GUI frontend for Pacman with complete AUR support.
    After install 'Add/Remove programs' and 'Software update' appears in Settings menu.

        pacman -Ss pamac
        
## To see all files installed with package:

        pacman -Ql package_name


## More programs

To get access to more packages install Trizen.
Trizen is a lightweight wrapper for pacman with user repositories. 

        pacman -S trizen

To find package or keyword releated with package type:

        trizen -Ss searchTerm

To install type (without sudo):

        trizen -S micro

## Appimages 

Appimages are self-contained one-file applications.

You can download appimages from: [AppImage Hub](https://www.appimagehub.com)


## Fixing some issues

Some packages (vagrant) require '/etc/arch-release' file to be in the system

        sudo touch /etc/arch-release 


---
