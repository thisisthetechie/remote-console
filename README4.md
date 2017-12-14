# Create a Logon Banner

To make this seem somewhat more professional, I added a banner to the logon process by creating a new file at `/etc/issue.net`
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
To enable the banner, I updated `/etc/ssh/sshd_config` to add the following line:
```
Banner /etc/issue.net
```

[Previous - Create a user account](README3.md) || [Next - Launch my VPN Server](README5.md)