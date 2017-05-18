---
layout: post
title: "Upgrading firmware on Cisco Small Business SG300 Series Managed Switch"
description: "Because I’ll never remember how I did this"
keywords: "cisco smb small-business switch firmware upgrade"
---

Because I’ll never remember how I did this!

# Prerequisite

As usual nothing is straight-forward ;)

## TFTP Server

Luckily on MacOSX it’s built in :)

To enable it you will need to use the following command:

```
sudo launchctl load -F /System/Library/LaunchDaemons/tftp.plist
```

Note: the -F is because it is disabled by default.

## Firewall

Since you’re running a server make sure your firewall isn’t blocking you!

# Download

https://software.cisco.com/download/navigator.html?mdfid=283009439&flowid=18905

Note: I’ve found all the port (e.g. sg300–10 vs sg300–28p) and power (POE) variations to be the same firmware. For example, sg300–10 is the same as sg300–28p. YMMV

### Warning: Firmware Format Changes!

If your firmware version is < 1.3.7.18 (sh ver) then you need to upgrade to that first as there was a change in both the firmware and boot formats!
If you don’t you will see the following error in the web console:

> entry already exists in the Copy History table...

And the following in the CLI or logs:

> %TFTP-A-TftpTxERROR: An error message was sent: 0 <Closed by application>

Then shell onto the switch and copy the firmware over:

Note: While I love the new web interface on these switches I still do this CLI.

```
switch# copy tftp://A.B.C.D/sx300_fw-1413.ros image
```

Finally, you will need to activate the new image and reboot the switch:

```
switch#sh bootv
Image  Filename   Version     Date                    Status
-----  ---------  ---------   ---------------------   -----------
1      image-1    1.3.7.18    12-Jan-2014  18:02:59   Active*
2      image-2    1.4.1.3     29-Mar-2015  16:24:16   Not active
switch#boot system image-2
switch#reload
```

# Cleanup

You’re running a server and your firewall is probably disabled...**FIXIT!**

## TFTP Server

To shutdown on MacOSX use the following:

```
sudo launchctl unload /System/Library/LaunchDaemons/tftp.plist
```

## Firewall

Close any ports opened in Prerequisites; above.

# References

This blog post is just a combination of the following:

* <http://aplawrence.com/MacOSX/tftp.html>
* <http://cmc.site11.com/2014/01/how-to-upgrade-firmware-and-boot-image-on-cisco-sg300-switch/>
