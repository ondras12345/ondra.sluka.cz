+++ 
draft = false
date = 2020-11-24T22:27:50+02:00
title = "Getting Screenshots from an Agilent 54621A Oscilloscope Via RS-232"
description = ""
slug = ""
authors = []
tags = ["oscilloscope"]
categories = ["electronics"]
externalLink = ""
series = []
+++
{{< figure src="scope-screenshot.png" alt="An example screenshot retrieved from the Agilent 54621A DSO over RS-232" >}}

My Agilent 54621A DSO has a broken floppy disk drive.
Instead of trying to repair it, I took a look at the Programmer's Guide for
the oscilloscope and found out I could download the screenshots over RS-232.

As usual, someone has already done a great job documenting the process of
[communicating with the oscilloscope from a Python script](https://01001000.xyz/2020-05-07-Walkthrough-Agilent-Oscilloscope-RS232/).
However, one of the things he does not mention is the pinout of the RS-232
cable you need to connect to the oscilloscope.
Fortunately, the User's Guide contains this handy diagram:

{{< figure src="agilent-pinout.png" width="600" caption="Agilent 54621A oscilloscope RS-232 connection cable pinout (<cite>Agilent 54621A User's Guide</cite>, Agilent Technologies, 2000)" >}}

My proof-of-concept script for retrieving a BMP screenshot:
```python3
#!/usr/bin/env python3
# pip3 install pyvisa
# pip3 install pyvisa-py

import pyvisa
from pyvisa import constants
import argparse

def main():
    parser = argparse.ArgumentParser()
    parser.add_argument('--device', '-D', default='/dev/ttyUSB1',
        help='Serial port the scope is attached to (default /dev/ttyUSB1)')

    parser.add_argument('file',
        help='File to write the output to')

    global args
    args = parser.parse_args()

    if not args.file.endswith('.bmp'):
        args.file = args.file + '.bmp'

    rm = pyvisa.ResourceManager()

    instr = rm.open_resource('ASRL/dev/ttyUSB1::INSTR', baud_rate=57600,
        flow_control=constants.VI_ASRL_FLOW_DTR_DSR,
        write_termination = '\n', read_termination='\n',
        timeout=45000)

    res = instr.query_binary_values(":DISPlay:DATA? BMP",
        datatype='B', container=bytearray)

    with open(args.file, "wb") as f:
        f.write(res)

    print("Screen image written to {}".format(args.file))

    rm.close()

if __name__ == '__main__':
    main()
```

{{< todo >}}
TODO new script? + imagemagick png
{{< /todo >}}

The main disadvantage is that this operation takes about 30 seconds to
complete.
However, I don't think you could get much faster than this with a floppy.

I plan on making a Github repository with this script after I do further
testing and make some improvements.
