+++ 
draft = false
date = 2023-03-20T14:57:35+01:00
title = "latexmk configuration for compiling gnuplottex figures using epslatex terminal"
description = ""
slug = ""
authors = []
tags = ["latexmk", "gnuplot"]
categories = ["LaTeX"]
externalLink = ""
series = []
math = true
+++
When I used `gnuplot` with $\LaTeX$ in the past, I was always using a custom
`Makefile` to compile the `gnuplot` figures. I recently came across the
[`gnuplottex` package](https://ctan.org/pkg/gnuplottex) and wanted to give it
a try.

I quickly came across a problem: when using the `epslatex` terminal, `latexmk`
would end up in an infinite loop trying to compile the document. I prepared a
demo for this:
```latex
\documentclass{standalone}
\usepackage{color}
\usepackage{gnuplottex}

\begin{document}
\noindent
\begin{gnuplot}[terminal=epslatex]
    set samples 100
    system("sleep 1.2")  # wait 1.2 seconds
    plot [0:2*pi] sin(x)
\end{gnuplot}
\end{document}
```

```console
$ latexmk --version

Latexmk, John Collins, 20 November 2021. Version 4.76

$ cat latexmkrc
$pdf_mode = 1;
$pdflatex = 'pdflatex --shell-escape %O %S';

$ latexmk demo
...
Rule 'pdflatex': File changes, etc:
   Changed files, or newly in use since previous run(s):
      'demo-gnuplottex-fig1.eps'
Latexmk: Maximum runs of pdflatex reached without getting stable files
...
```

The problem is that the generated `.eps` file contains a timestamp, so its
hash keeps changing:
```console
$ grep 2023 demo-gnuplottex-fig1.eps
%%CreationDate: Mon Mar 20 16:25:44 2023
  /CreationDate (Mon Mar 20 16:25:44 2023)
```

I have fixed this problem by adding extra configuration to my `latexmkrc`.
I have also added `clean_ext` to clean up `gnuplottex`-generated files when
running `latexmk -c`:
```
$pdf_mode = 1;
# enable shell escape - needed for gnuplottex
$pdflatex = 'pdflatex --shell-escape %O %S';

# gnuplottex would make latexmk go into an infinite loop
$hash_calc_ignore_pattern{'eps'} = '^%%CreationDate: |CreationDate \(';

# gnuplottex cleanup
$clean_ext = "$clean_ext %R.gnuploterrors %R-gnuplottex-*";
```
