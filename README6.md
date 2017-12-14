# Configure SSH

The simplest way to create a connection to my laptop is to script the Bash Login (`/home/consoleuser/.bashrc`) to simply send the user into another SSH connection. 

To do this, I created the same user on my VPN Server:
```bash
useradd -m -d /home/consoleuser consoleuser
passwd consoleuser
# Enter the same password for the user as used on the laptop
```

[Previous - Launch my VPN Server](README5.md) || [Next](README7.md)