# Artix Linux - Maintenance

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


   
---