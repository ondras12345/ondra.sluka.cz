+++ 
draft = false
date = 2020-11-27T12:01:00+02:00
title = "Microsoft Teams Keeps Listening After You Hang Up!"
description = ""
slug = ""
authors = []
tags = ["GNU/Linux"]
categories = ["software"]
externalLink = ""
series = []
+++
I have recently noticed that Microsoft Teams in GNU/Linux kept listening to my
microphone after I hung up a call. The only way to stop this seems to be
exiting Teams and starting it again.

There is a
[report at the uservoice user feedback forum for Teams](https://microsoftteams.uservoice.com/forums/555103-public/suggestions/41257648-teams-keeps-microphone-open),
but for some reason it is in the "Public" category instead of "Bug Reports".
There is also a
[related thread on Microsoft's website](https://docs.microsoft.com/en-us/answers/questions/65290/teams-keeps-microphone-open.html).

If you have a machine that is running GNU/Linux and are forced to use
Microsoft Teams, please **test this on your computer** and **vote on the
report**. I've added a comment to it, the text is replicated below:
> Teams 1.3.00.25560 (64 bits) on Ubuntu 20.04.1
> 
> Steps to reproduce:
> 1. make a test call (enter /testcall to the search box in Teams)
> 2. hang up
> 
> 3. list listening applications
> ```
> $ pacmd list-source-outputs
> 1 source output(s) available.
>     index: 0
> 	driver: 
> 	flags: START_CORKED 
> 	state: CORKED
> 	source: 2 
> 	volume: mono: 65536 / 100% / 0,00 dB
> 	        balance 0,00
> 	muted: no
> 	current latency: 0,00 ms
> 	requested latency: 10,00 ms
> 	sample spec: s16le 1ch 44100Hz
> 	channel map: mono
> 	             Mono
> 	resample method: speex-float-1
> 	owner module: 13
> 	client: 12 
> 	properties:
> 		window.icon_name = "skype"
> 		application.icon_name = "skype"
> 		media.role = "phone"
> 		media.name = "Input"
> 		application.name = "Skype"
> 		native-protocol.peer = "UNIX socket client"
> 		native-protocol.version = "33"
> 		application.process.id = "11879"
> 		application.process.user = "myUsername
> 		application.process.host = "myPC"
> 		application.process.binary = "teams"
> 		application.language = "en_US.UTF-8"
> 		window.x11.display = ":0"
> 		application.process.machine_id = "someid"
> 		module-stream-restore.id = "source-output-by-media-role:phone"
> ```
