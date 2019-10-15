# Artix Linux - Headless Usage (for fun)

(requires base install) Archlinux with OpenRC guide 

---

## Intro

Basically here we will try to use terminal for fun.  
First install Trizen to get access to more programs.

    sudo pacman -S trizen    

## Web Browser

![alt text](https://github.com/damir-sijakovic/artix/blob/master/files/links.png)

Links is text web browser.  

        sudo pacman -S links
           
To use run:

        links google.com
                
## File Manager

![alt text](https://github.com/damir-sijakovic/artix/blob/master/files/mc.png)

GNU Midnight Commander is a visual file manager.
It's a feature rich full-screen text mode application that allows you 
to copy, move and delete files and whole directory trees, search for
files and run commands in the subshell. Internal viewer and editor are included. 

        sudo pacman -S mc   
        
        
## Video Player

![alt text](https://github.com/damir-sijakovic/artix/blob/master/files/mplayer.png)
        
To watch videos in terminal we will use framebuffer and mplayer. 
First install:

    sudo pacman -S mplayer xf86-video-fbdev   
        
Then run:        
        
    mplayer -vo fbdev2 file.mp4   
        

## Text Tools

![alt text](https://github.com/damir-sijakovic/artix/blob/master/files/texttools.png)

Figlet will display text in ascii art style while ponysay will draw a pony.

    pacman -S figlet ponysay
    
    
## Termboy

![alt text](https://github.com/damir-sijakovic/artix/blob/master/files/termboy.png)

Termboy is a Nintendo Game Boy emulator...for your terminal.
Termboy is based on Higan, but uses ncurses to render the screen.
    
To install type:

    trizen -S termboy

To run:

    termboy wario3.gb
 
 
 
           
