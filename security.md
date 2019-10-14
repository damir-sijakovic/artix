# Artix Linux - Security

Archlinux with OpenRC guide 

---

## Firewall

We will handle firewall with rules scripts and we will run them as startup scripts.
Rules are in '/etc/iptables/' and startup scripts are in '/etc/local.d/' directory.
So first we are going to create drop iptables rules script.

        sudo nano /etc/iptables/drop.rules

Then add these lines to file:

        # drop (block all traffic)
        *filter
        :INPUT DROP [0:0]
        :FORWARD DROP [0:0]
        :OUTPUT DROP [0:0]
        COMMIT
        
Now create startup script:

        sudo nano /etc/local.d/iptables.start

Then add these lines to file (note that these rules scripts are just exemplary):

        /sbin/iptables-restore < /etc/iptables/simple_firewall.rules
        /sbin/ip6tables-restore < /etc/iptables/drop.rules

And make script executable:

        sudo chmod +x /etc/local.d/iptables.start
        
Reboot and run to test:

        sudo iptables -L
        sudo ip6tables -L
        
To get more rules scripts, google for "iptables rules github". Create/copy in '/etc/iptables/' and do as above.   
      
## Firejail

Firejail is a SUID program that reduces the risk of security breaches by restricting the running 
environment of untrusted applications using Linux namespaces and seccomp-bpf. 

Install:

        pacman -S firejail
        
To run program without internet access:

        firejail --net=none firefox &
        

To force Firefox to use Google's dns use:

        firejail --net=eth0 --dns=8.8.8.8 firefox &

To make Firefox use new directory to store settings:

        firejail --private=/home/username/new_settings firefox -no-remote &
      
[More examples](https://firejail.wordpress.com/documentation-2/basic-usage/)
        
---
