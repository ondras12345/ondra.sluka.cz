+++ 
draft = false
date = 2021-01-17T00:30:57+02:00
title = "Making a Cool Favicon for the Website"
description = ""
slug = ""
authors = []
tags = ["IT", "electronics"]
categories = []
externalLink = ""
series = ["favicon"]
+++
{{< figure-resize resize="1024x1024" src="featured.jpg" width="100%" >}}

[`favicon.ico`](https://en.wikipedia.org/wiki/Favicon) is the icon you see in
the browser tab next to the title of the webpage.
It is a file containing one or more images.
For example Wordpress requires you to supply it with a single image greater
than 512 × 512 px.

I didn't really have an idea of what the icon should look like, so I decided
to limit my choices by displaying it on an oscilloscope in XY mode.
I made a basic XY drawing on a piece of squared paper and deduced the
$X=f(t)$ and $Y=f(t)$ waveforms.

{{< figure-resize src="favicon-audacity.png" caption="The waveforms I used to generate my favicon. Screenshot from Audacity." >}}

I then created a simple Python script `favicon.py` that generates
a [WAV](https://en.wikipedia.org/wiki/WAV) file with the waveforms—$X$ on
the left channel and $Y$ on the right channel.
It should be possible to create the same waveform
in [Audacity](https://www.audacityteam.org/), but I just find the Python way
much easier.

```python3
#!/usr/bin/env python3

# https://stackoverflow.com/questions/33879523/python-how-can-i-generate-a-wav-file-with-beeps
import math
import wave
import struct

L = []
R = []
sample_rate = 44100.0
# I will not utilize the whole 10x8 screen because I want the circle to still
# be a circe even with the vernier control in the CAL position.
x_div = 1.0/4
y_div = 1.0/4
time_div = 1


def append_sine(audio, duration_milliseconds, duration_deg=360, phase=0,
                amplitude=1.0):

    num_samples = duration_milliseconds * (sample_rate / 1000.0)

    freq = duration_deg / 360 / (duration_milliseconds / 1000)
    phase_rad = phase / 180 * math.pi

    for i in range(int(num_samples)):
        audio.append(amplitude * math.sin(2 * math.pi * freq *
                                          i / sample_rate +
                                          phase_rad))

    return


def append_ramp(audio, duration_milliseconds, minval, maxval):
    num_samples = duration_milliseconds * (sample_rate / 1000.0)
    step = (maxval - minval) / num_samples
    val = minval
    for i in range(int(num_samples)):
        audio.append(val)
        val += step


def save_wav(file_name):
    wav_file = wave.open(file_name, "w")

    # wav params
    nchannels = 2
    sampwidth = 2

    if len(L) != len(R):
        raise Exception('Left and right channels are not the same length')

    nframes = len(L)
    comptype = "NONE"
    compname = "not compressed"
    wav_file.setparams((nchannels, sampwidth, sample_rate, nframes, comptype,
                        compname))

    # WAV files here are using short, 16 bit, signed integers for the
    # sample size.  So we multiply the floating point data we have by 32767,
    # the maximum value for a short integer.
    # NOTE: It is theortically possible to use the floating point -1.0 to 1.0
    # data directly in a WAV file but not obvious how to do that using the
    # wave module in Python.
    for (sampleL, sampleR) in zip(L, R):
        wav_file.writeframes(struct.pack('h', int(sampleL * 32767.0)))
        wav_file.writeframes(struct.pack('h', int(sampleR * 32767.0)))

    wav_file.close()

    return


if __name__ == '__main__':
    append_ramp(L, 3.5*time_div, -4*x_div, -0.5*x_div)
    append_sine(L, 2*time_div, duration_deg=540, phase=-90, amplitude=0.5*x_div)
    append_ramp(L, 3.5*time_div, 0.5*x_div, 4*x_div)

    append_sine(R, 3.5*time_div, duration_deg=180, amplitude=y_div)
    append_sine(R, 2*time_div, duration_deg=540, amplitude=0.5*y_div)
    append_sine(R, 3.5*time_div, duration_deg=180, amplitude=y_div)

    save_wav("favicon.wav")
```

Wanna hear what the favicon sounds like? You can play it here or
[download the audio file](favicon-5s.wav).
**WARNING**: This will be **loud**. I used the whole vertical resolution
available.

{{< audio src="favicon-5s.wav" >}}

I also created another small program, `wavxy.py`, that takes a short WAV file
and plots its contents in XY mode using
[`matplotlib`](https://matplotlib.org/).

```python3
#!/usr/bin/env python3

# This program loads the whole wav file into memory.
# Do not use for large files!

import wave
import argparse
import struct
import matplotlib.pyplot as plt
import matplotlib.ticker as plticker


def main():
    parser = argparse.ArgumentParser()
    parser.add_argument('file', help='wav file to read data from')

    args = parser.parse_args()

    wav_file = wave.open(args.file, "r")

    if wav_file.getnchannels() != 2:
        raise Exception("Only stereo tracks are supported."
                        " nchannels: {}".format(wav_file.getnchannels()))

    sample_width = wav_file.getsampwidth()
    if sample_width != 2:
        raise Exception("Only sample width = 2 is supported."
                        " Got {}.".format(sample_width))

    fig, ax = plt.subplots()

    Xs = []
    Ys = []
    for i in range(wav_file.getnframes()):
        frame = wav_file.readframes(1)
        (X, Y) = tuple(ti / 32767.0 for ti in struct.unpack("<2h", frame))
        Xs.append(X)
        Ys.append(Y)

    wav_file.close()
    ax.plot(Xs, Ys)
    ax.set_xlim([-1.25, 1.25])
    ax.set_ylim([-1, 1])
    loc = plticker.MultipleLocator(base=1.0/4)
    ax.xaxis.set_major_locator(loc)
    ax.yaxis.set_major_locator(loc)
    ax.xaxis.set_ticklabels([])
    ax.yaxis.set_ticklabels([])
    ax.set_aspect('equal', adjustable='box')
    ax.grid()
    plt.show()


if __name__ == '__main__':
    main()
```

{{< figure-resize src="favicon-wavxy.png" caption="A plot of my favicon as generated by `wavxy.py`" width="50%" >}}

To play the `favicon.wav` file, I used Audacity.
Just press `shift+space` to get it to play the audio in an endless loop.

The sound card of the computer I have in my lab seems to invert the signal it
plays back, so I used the Effect&gt;Invert function in Audacity to get it
right.
Unfortunately, the sound card outputs the signal through some AC coupling
capacitors and thus removes the DC offset of the waveform.
It also seems to distort the waveform, as shown by the following figure.

{{< figure-resize src="favicon-scope-distorted.jpg" caption="The distorted favicon" width="70%" >}}

I had to use another computer to get a reasonably good-looking image.

{{< figure-resize src="favicon-scope-edit.jpg" width="70%" >}}

## The actual icon
I cannot use the photo as the favicon as it would be too small to recognize
and just look like garbage. I knew this before I started this project and I
planned to use the `matplotlib` plot instead. I used a slightly modified
version of the `wavxy.py` program above to generate it. I then edited it with
Inkscape, added a background color that matches the
[Dracula theme](https://draculatheme.com/contribute), and uploaded it
as a png file to my server.

{{< figure src="/images/favicon-300x300.png" width="300" >}}
