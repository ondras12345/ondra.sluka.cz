+++ 
draft = false
date = 2023-06-13T14:13:21+02:00
title = "Mikrotik script to send e-mail notifications when RouterOS update is available"
description = ""
slug = ""
authors = []
tags = ["Mikrotik"]
categories = ["software"]
externalLink = ""
series = []
+++

I use this simple script on all the Mikrotik routers I manage to notify me
when a new update is available.
```
/system script
add dont-require-permissions=no name=check-for-updates owner=admin policy=\
    read,write,policy,test source="# Check for updates and mail admin if available\r\
    \n:local emailAddress \"example@example.com\"\r\
    \n\r\
    \n/system package update\r\
    \nset channel=stable\r\
    \ncheck-for-updates\r\
    \n\r\
    \n:local currver [get installed-version]\r\
    \n:local newver [get latest-version]\r\
    \n:local lastnotifver [/system script get check-for-updates comment]\r\
    \n:if (\$currver != \$newver and \$newver != \$lastnotifver) do={\r\
    \n:log info \"Software update available (\$currver -> \$newver). Sending email...\"\r\
    \n:local hostname [/system identity get name]\r\
    \n/tool e-mail send to=\"\$emailAddress\" subject=\"[\$hostname] Software Update Available: \$newver\" \\\
    \r\
    \n    body=\"A new update is available for your MikroTik device: \$currver -> \$newver\"\r\
    \n/system script set check-for-updates comment=\"\$newver\"\r\
    \n}"

/system scheduler
add interval=1d name=check-for-updates on-event=check-for-updates policy=read,write,policy,test \
    start-date=mar/12/2020 start-time=16:00:00
```

There isn't much going on, apart from the variable `lastnotifver`. The script
uses its own comment to persist information about the last version a
notification was sent about. This is done to prevent spamming the admin with
daily e-mails if he decides to skip a version.

The scheduler rule just executes the script daily at 16:00.
