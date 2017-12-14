# remote-console
For more information about this project, visit the blog article at https://linkedin.com/pulse/creating-remote-console-solution-pete-mallam

## Getting Started
The first thing you will need is a laptop (in my case, an old 32-bit Dell Inspiron Mini 10) with a working Linux install. 
If you want to enable GUI, then go ahead. My installation was all CLI.

I think the very first thing I was forced to do was install the drivers for the Wireless LAN interface, which is an old complaint about Linux.
I won't go into that installation, as it may not be relevant to everyone. If there are any requests, I can write that up as another article. 

_My linux of choice is Debian so all commands may differ on your own distribution of choice, if you have any questions please ask away_

### Install your TTY Console
I used Minicom, a simple TTY Console that I found out about via a couple of Google Searches for the best way to connect to a Cisco 1400 Router.

_To simplify things, switch to **root**:_```sudo su```
```bash
apt-get update && apt-get -y upgrade
apt-get install -y minicom
```

To configure Minicom, run the command `sudo minicom -s` and you will see a menu like this:
```
            +-----[configuration]------+
            | Filenames and paths      |
            | File transfer protocols  |
            | Serial port setup        |
            | Modem and dialing        |
            | Screen and keyboard      |
            | Save setup as dfl        |
            | Save setup as..          |
            | Exit                     |
            | Exit from Minicom        |
            +--------------------------+
```
Assuming you have a USB Console or USB to Serial cable, select **Serial port setup** and configure:
```
    +-----------------------------------------------------------------------+
    | A -    Serial Device      : /dev/ttyUSB0                              |
    | B - Lockfile Location     : /var/lock                                 |
    | C -   Callin Program      :                                           |
    | D -  Callout Program      :                                           |
    | E -    Bps/Par/Bits       : 9600 8N1                                  |
    | F - Hardware Flow Control : No                                        |
    | G - Software Flow Control : No                                        |
    |                                                                       |
    |    Change which setting?                                              |
    +-----------------------------------------------------------------------+
```
_To make a change, enter the letter (eg. A) and follow the instructions._

### Create a user account
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
1. Open the Sudo Config file: ```visudo```
2. Add the following line to the end: ```consoleuser  ALL = NOPASSWD: /usr/bin/minicom```

I wanted to block the user from logging in if the Console Cable is not connected, thus ensuring that the only time the user can be logged on is when I'm onsite.

To do this, I added the following lines to ```/home/consoleuser/.bashrc```
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

## Create a Logon Banner

To make this seem somewhat more professional, I added a banner to the logon process by creating a new file at ```/etc/issue.net```
```
   -------------------------------------------
   ----------------  WARNING  ----------------
   -------------------------------------------

This is NOT a public system - Authorized use only!

          The Techie IT Services Ltd
          ==========================

Protected system, your activity will be monitored,
any unrecognised activity will be captured and may
be reported to the authorities for further action.

To continue, please enter your password:
```
To enable the banner, I updated ```/etc/ssh/sshd_config``` to add the following line:
```
Banner /etc/issue.net
```
## Launch my VPN Server
I'm using OpenVPN for this solution, and to keep things simple you can use the pre-configured OpenVPN Appliance on AWS. It comes with two 'admin' licenses so you shouldn't need more than that.

Once launched, you will need to configure it.
