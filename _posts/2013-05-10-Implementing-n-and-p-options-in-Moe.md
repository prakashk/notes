Title: Implementing -n and -p options in Moe  
Date: Fri May 10 11:00:03 2013  
Tags: moe, perl

Perl command-line options `-n` and `-p` are quite useful, especially
in one-liners to process a bunch of files quickly. These options wrap
the code to be executed in a `while (<>)` loop. Perl iterates through
the files named as command-line arguments, opens each file and
executes the code for each line of the file. The input line is made
available in the topic variable `$_`. This eliminates lot of
boiler-plate code and let's the programmer concentrate on the
important part: how each input line should be processed.

The difference between `-n` and `-p` is that using the `-p` option
prints to `STDOUT` the content of `$_` at the end of each iteration of
the `while` loop. This is very useful when one wants to process every
input line in some way and print the result. The `-n` option is
useful, when only a subset of the input lines are needed, but printing
of desired output lines must be done explicitly.

There is another option `-i` where the files are modified in-place
instead of sending the processed data to STDOUT. Let's ignore this
option for now.

Here's a quick example that prints those of the input lines matching
certain pattern.

    perl -ne 'print if /interesting/' file1 file2 ... fileN;
    
This is equivalent to running the following Perl code:

    while (<>) {
        print if /interesting/;  # code for each line
    }
    
A lot is going on here in this concise piece of code. The use of
diamond operator `<>` with no filehandle is equivalent to the
following psuedo-code:

    unshift(@ARGV, '-') unless @ARGV;
    while ($ARGV = shift) {
        open(ARGV, $ARGV);
        while (<ARGV>) {
            print if /interesting/;  # code for each line
        }
    }

In order to implement just this behavior in Moe, we need to:

* Implement filehandles and the diamond operator, or something
  equivalent to be able to iterate through the lines of input file and
  assigns each line to `$_`.
  
* Set the input line number to the special variable `$.`.

* Implement special blocks (subroutines) `BEGIN` and `END`, which are
  executed *before* and *after* the other code is executed. Another
  wrinkle here is that we can have multiple `BEGIN` and `END` blocks
  in a program. The `BEGIN` blocks are executed in the order
  defined, whereas `END` blocks are executed in reverse (first `END`
  block is executed last.)
  
What else?
