---
draft: false
date: 2025-11-03T09:42:26+01:00
title: 'Making LaTeX interpreter work in GNU Octave Flatpak'
description: >-
  A simple workaround that allows GNU Octave Flatpak to access a LaTeX
  compiler installed via apt.
featured_image: ""
tags:
  - Linux
categories:
  - software
  - LaTeX
series:
math: true
---
On my Ubuntu 24.04.3 system, I have a LaTeX compiler installed:
```sh
sudo apt install texlive
```

I also have GNU Octave v10.2.0 installed as a flatpak.
I needed the occasional $\hat{x}$ and $\tilde{x}$ in a plot `ylabel`.
The built-in `"tex"` intepreter cannot do this, so I needed
the
[`"latex"` interpreter](https://docs.octave.org/v10.2.0/_0022latex_0022-interpreter.html)
which works by running an actual $\LaTeX$ compiler.
```matlab
xlabel('$\tilde{x}$', 'Interpreter', 'latex')
```

However, the Flatpak sandbox prevents Octave from running other programs.

## Solution
This is how I made it work on my system. I am not claiming this is the best
fix, but it seems to work. Note that this punches a huge hole through the
Flatpak sandbox, but I don't care about that.
```sh
flatpak override --user \
  --env=OCTAVE_LATEX_BINARY=$HOME/bin/flatpak-latex \
  --env=OCTAVE_DVIPNG_BINARY=$HOME/bin/flatpak-dvipng \
  --env=OCTAVE_DVISVG_BINARY=$HOME/bin/flatpak-dvisvgm \
  org.octave.Octave

flatpak override --user --talk-name=org.freedesktop.Flatpak org.octave.Octave
flatpak override --user --filesystem=/tmp org.octave.Octave

cat > ~/bin/flatpak-latex <<"EOF"
#!/bin/sh
prog=""
if [ "$FLATPAK_ID" != "" ]; then
  prog="flatpak-spawn --host "
fi
$prog latex "$@"
EOF
chmod +x ~/bin/flatpak-latex

cat > ~/bin/flatpak-dvipng <<"EOF"
#!/bin/sh
prog=""
if [ "$FLATPAK_ID" != "" ]; then
  prog="flatpak-spawn --host "
fi
$prog dvipng "$@"
EOF
chmod +x ~/bin/flatpak-dvipng

cat > ~/bin/flatpak-dvisvgm <<"EOF"
#!/bin/sh
prog=""
if [ "$FLATPAK_ID" != "" ]; then
  prog="flatpak-spawn --host "
fi
$prog dvisvgm "$@"
EOF
chmod +x ~/bin/flatpak-dvisvgm
```
