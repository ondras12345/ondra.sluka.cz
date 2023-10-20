---
draft: false
date: 2023-10-20T12:35:26+02:00
title: "Lissajous Figures in Gnuplot"
description: >-
featured_image: "featured.png"
tags:
  - gnuplot
categories:
  - software
  - LaTeX
series:
math: true
---
{{< figure src="featured.png" width="100%" >}}

A few years ago, I have created a gnuplot script that draws a bunch of
[Lissajous figures](https://en.wikipedia.org/wiki/Lissajous_curve) with
different parameters.

It is made to be incorporated in a $\LaTeX$ document and should be fairly
easy to modify.

Source code: [`lissajous.gpi`](lissajous.gpi),
[`lissajous-demo.tex`](lissajous-demo.tex)


## Compiling
Download the source code files and run the following commands to generate
[`lissajous-demo.pdf`](lissajous-demo.pdf):
```sh
gnuplot lissajous.gpi
pdflatex lissajous-demo.tex
```
