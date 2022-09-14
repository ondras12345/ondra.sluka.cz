---
draft: false
title: "Meratronik G 432 Function Generator"
date: 2022-09-10T18:07:09+02:00
description: "1&nbsp;MHz analog function generator"
featured_image: "Meratronik-G-432.jpg"
tags: ["function generator", "teardown"]
categories: ["electronics"]
---
There doesn't seem to be much information about the Meratronik G&nbsp;432
function generator available on the Internet, so I decided to share some of
my teardown pictures.

{{< figure-resize src="Meratronik-G-432.jpg" width="100%"
    alt="Front panel of Meratronik G 432 analog function generator"
>}}


## Useful links
- [Service manual](http://bee.mif.pg.gda.pl/ciasteczkowypotwor/Polskie%20mierniki/Moje/G432.pdf)
- [Specifications](http://delibra.bg.polsl.pl/Content/7874/GF_G-432.pdf)


## Teardown
The power cord on my unit is not original, I have replaced it with a 3-prong
one in order to ground the instrument. There was quite a high voltage between
the chassis of the unit and mains earth without the PE connection.

{{< image-gallery-list "Internals of a disassebled Meratronik G 432 function generator"
    "Meratronik-G-432-teardown-1.jpg" "Meratronik-G-432-teardown-2.jpg" "Meratronik-G-432-teardown-3.jpg" "Meratronik-G-432-teardown-4.jpg"
>}}

As you can see, the unit is fully analog. Because the maximum frequency it can
generate is only 1&nbsp;MHz (it can do square wave, so the required bandwidth
is higher than this, but still pretty low), there isn't anything interesting
from the RF / transmission line standpoint inside.

The traces on the PCB, on the other hand, look pretty interesting.
Unfortunately, I haven't taken a photo of them, but they all look hand-drawn
with nice curves &mdash; none of that right angle rubbish.


{{< todo >}}
## Modifications
line cord mod
{{< /todo >}}
