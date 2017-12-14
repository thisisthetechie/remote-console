### Install your TTY Console
I used Minicom, a simple TTY Console that I found out about via a couple of Google Searches for the best way to connect to a Cisco 1400 Router.

_To simplify things, switch to **root**:_`sudo su`
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

[Previous](README.md) || [Next](README3.md)