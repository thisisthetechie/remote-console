# Create a user account
In order to improve security, I created a new user and locked it down as best I could. 
The first step is to create a new user:
```bash
useradd -m -d /home/consoleuser consoleuser
passwd consoleuser
# Enter new password for the user
```
Add the user to the **sudo** group (to allow sudo access)
```bash
usermod -a -G sudo consoleuser
```
Update the sudoers file to allow access to minicom _without_ needing to re-enter a password
1. Open the Sudo Config file: `visudo`
2. Add the following line to the end: `consoleuser  ALL = NOPASSWD: /usr/bin/minicom`

I wanted to block the user from logging in if the Console Cable is not connected, thus ensuring that the only time the user can be logged on is when I'm onsite.

To do this, I added the following lines to `/home/consoleuser/.bashrc`
```bash
echo "****** CHECKING FOR CONSOLE CONNECTION ******"
if [ -e "/dev/ttyUSB0" ]
then
        sudo minicom
else
        echo No console connection found, disconnecting
        exit
fi

while true
do
        ps aux | grep minicom > /dev/null
        if [ $? -eq 0 ]
        then
                exit 5
        fi
done
```
This additional script (run when that particular user logs onto the system) will check for /dev/ttyUSB0 (the console cable) and will not allow the connection to proceed if not found.
If the console cable _is_ found it will launch Minicom and then wait - keeping an eye on Minicom so that it can terminate the connection when/if Minicom is closed.

The reason for this is to ensure access is **_only_** for using the remote console.

[Previous - Install the TTY Console](README2.md) || [Next - Create a Logon Banner](README4.md)