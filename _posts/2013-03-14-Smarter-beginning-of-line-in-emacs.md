Title: Smarter beginning-of-line in emacs  
Date: Thu Mar 14 10:39:20 2013  
Tags: emacs

A while ago, I learned about an Emacs function called
`back-to-indentation` which is bound to <kbd>M-m</kbd> by
default. It moves the cursor to the first non-whitespace character on
the current line. This is quite useful while coding to edit text at or
near the beginning of line ignoring the indentation. The problem is I
can't seem to remember this function, and I always press
<kbd>C-a</kbd> (`move-beginning-of-line`), followed by
<kbd>C-right</kbd> and then <kbd>C-left</kbd>, which takes me to the
first non-whitespace character. Pretty inefficient way of doing it,
but it is now in my muscle memory.

We can improve it slightly by replacing the last two key presses by a
single <kbd>TAB</kbd>. But, I don't do this often either.  Clearly,
there should be a better way, considering this is Emacs we are talking
about.

I vaguely remember seeing some function that someone wrote that
redefined <kbd>C-a</kbd>. I couldn't find it anywhere in my notes, so
here's my attempt at writing it myself.

    ;;; smarter beginning-of-line

    (defun smart-beginning-of-line ()
      "Move to beginning of line. If point is already at beginning of
       line, move to first non-whitespace character on the line."
      (interactive)
      (if (bolp)
          (back-to-indentation)
        (move-beginning-of-line 1)))

    (global-set-key [remap move-beginning-of-line] 'smart-beginning-of-line)

The last line remaps the current key bound to the
`move-beginning-of-line` (or <kbd>C-a</kbd>) to the newly defined
function. So, pressing <kbd>C-a</kbd> once takes me to beginning of
the line as usual. Press it twice and cursor is at the first text
character on the line. Even though it is two keystrokes, it is faster
than pressing <kbd>C-a</kbd> and <kbd>TAB</kbd>.
