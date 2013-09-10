Title: Stupid PDF tricks  
Date: Tue Sep 10 11:32:09 2013  
Tags: pdf, tips

## Determine margins with Ghostscript

There is a special bbox "device" that just prints the bounding box of each page.

    $ gs -q -dSAFER -dBATCH -dNOPAUSE -sDEVICE=bbox input.pdf

Output:

    %%BoundingBox: 281 465 461 663
    %%HiResBoundingBox: 281.321991 465.191986 460.565986 662.219980
    %%BoundingBox: 138 481 329 663
    %%HiResBoundingBox: 138.635996 481.751985 328.841990 662.219980

We are interested only in `BoundingBox` lines. Pipe the command through `grep -v` to get rid of the HiResBoundingBox lines. The information is printed on `stderr`, so before piping, it should be redirected to `stdout`.

    $ gs -q -dSAFER -dBATCH -dNOPAUSE -sDEVICE=bbox input.pdf 2>&1 \|
        grep -v HiResBoundingBox \|
        cat -n

Output:

     1	%%BoundingBox: 281 465 461 663
     2	%%BoundingBox: 138 481 329 663

## Crop white margins using `pdfcrop` (part of `texlive-extra-utils` package on debian)

    $ pdfcrop input.pdf input-cropped.pdf

Output is fully cropped file with no white margins.

    $ pdfcrop --margins 20 input.pdf input-cropped.pdf

This results in every page having a margin of 20 pixels on all four sides. If specific margins are desired:

    # <right> is same as <left> and <bottom> is same as <top>
    $ pdfcrop --margins "<left> <top>"

    $ pdfcrop --margins "<left> <top> <right> <bottom>"

`pdfcrop` uses `ghostscript` for calculating the bounding box information and then rewrites the input file with specified margins. By default, `pdftex` is used in writing the output file. Using the option `--xetex` makes use of `xetex` in writing the output file.

One downside is that the output file (when default `pdftex` is used) appears to be much larger than the input file in my limited testing. Using the `--xetex` option makes the output file still larger than the input, but significantly smaller than the default output. I tested on `onlisp.pdf` using both default and `--xetex` option. 

    $ ls -1sh onlisp*.pdf
     5.9M onlisp-cropped.pdf
     1.1M onlisp-cropped-xetex.pdf
    1012K onlisp.pdf

The `pdftex`-cropped version is 495% larger, while the `xetex`-cropped version is only 10% larger. Huge improvement. Comparing the output files did not reveal any noticeable differences in quality.

It did take a long time to produce the cropped file (13+ minutes). Thanks to the other `pdfcrop` [1] (by Eric Doviak), I discovered that `ghostscript` allows one to specify output resolution (`-r` option), which makes the calculation of the bounding box blazingly fast (in comparison). `pdfcrop` is written in Perl, so I thought it would be easy to patch it to include this option. Then I noticed that there is a `--resolution` option in `pdfcrop`. Using this option made a huge difference.

    $ time pdfcrop --resolution 72 --xetex --margins 20 onlisp.pdf onlisp-cropped-xetex-r72.pdf
    PDFCROP 1.20, 2009/10/06 - Copyright (c) 2002-2009 by Heiko Oberdiek.
    ==> 426 pages written on `onlisp-cropped-pdftex-r72.pdf'.
     
    real	0m18.182s
    user	0m17.520s
    sys	0m0.680s

Yes. Adding the --resolution option makes the script run in a measly 18 seconds, as opposed to 13+ minutes! The bounding box values are slightly different when using the --resolution option, but so far I haven't noticed any issues because of that.

The version I tested with was `1.20` and is pretty old (2009). Latest version (currently 1.38) can be found at http://www.ctan.org/tex-archive/support/pdfcrop, and seems to add more options including support for `luatex`.
