+++ 
draft = false
date = 2021-01-18T23:57:00+02:00
title = "Drawing the Favicon on a C64"
description = ""
slug = ""
authors = []
tags = []
categories = ["software"]
externalLink = ""
series = ["favicon"]
+++
{{< figure src="favicon-C64.png" width="100%" >}}

This is a followup to my
[previous post](/posts/making-a-cool-favicon-for-the-website/), so make sure
to check that out first.

What would a favicon be good for if you couldn't display it on a Commodore 64?
Just kidding. I needed an excuse to practice my BASIC programming skills.
I don't actually own a C64 (yet), but I have a great
[C64 emulator](https://vice-emu.sourceforge.io/).

I had to post this in time, so everything is still quite messy. I might get to
cleaning it up a bit later. In fact, this is a good candidate for the worst
BASIC program ever&hellip;

```BASIC
10 POKE 646,3
20 PRINT CHR$(147)
30 PRINT "ONDRA.SLUKA.CZ/WP/ FAVICON         2021"
50 XCDIV=8
51 XDIV=40/XCDIV
55 YCDIV=4
56 YDIV=25/YCDIV
60 X=0
61 Y=0
100 FOR X=-4*XDIV TO -.5*XDIV
110 Y=YDIV*SIN(π*(-X-4*XDIV)/(-3.5*XDIV))
120 GOSUB 500
130 NEXT X
140 REM CIRCLE
150 FOR X=-.5*XDIV TO .5*XDIV
155 XI = X/XDIV
160 Y=.5*YDIV*SIN(ATN(XI/SQR(-XI*XI+1))+ π/2)
161 Y=INT(Y)
170 GOSUB 500
180 Y=-Y
190 GOSUB 500
200 NEXT X
300 FOR X=.5*XDIV TO 4*XDIV
310 Y=YDIV*SIN(π*(X-4*XDIV)/(-3.5*XDIV))
320 GOSUB 500
330 NEXT X
400 END
500 REM POINT TO SCREEN
510 YS=(-INT(Y)+YCDIV/2*YDIV)*40
520 POKE 1024+YS+INT(X),160
530 RETURN
```
