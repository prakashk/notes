Title: Emacs goto-line with feedback  
Date: Fri Dec 28 13:32:43 2012  
Tags: Emacs

Found this on Magnars'
[What the .emacs.d?](http://whattheemacsd.com/key-bindings.el-01.html)
blog:

When <kbd>M-g g</kbd> (`goto-line`) is pressed, it prompts for the
line number to go to. If line numbers are displayed in the current
buffer, it would be easy to find the number of the line to go to and
enter. If not, one might first enable line numbers, find the line
number to go to and run the function and then disable line numbers
again.

This function does away with the tedium of this process, and
temporarily enables line numbers when `goto-line` is run.

    (defun goto-line-with-feedback ()
      "Show line numbers temporarily, while prompting for the line number input"
      (interactive)
      (unwind-protect
          (progn
            (linum-mode 1)
            (goto-line (read-number "Goto line: ")))
        (linum-mode -1)))
        
Line numbers are turned off, once the function is completed.

That last part, however, is a minor problem, since the line numbers
are unconditionally turned off. Even if line numbers were on to begin
with. This is fixed with a slight change. Remember if the line-numbers
were on or off, and turn them on/off only when needed.

    (defun goto-line-with-feedback ()
      "Show line numbers temporarily, while prompting for the line number input"
      (interactive)
      (let ((line-numbers-off-p (not linum-mode)))
        (unwind-protect
            (progn
              (when line-numbers-off-p
                (linum-mode 1))
              (call-interactively 'goto-line))
          (when line-numbers-off-p
            (linum-mode -1)))))

That's it.

Another very useful tip I found from the same post:

    (global-set-key [remap goto-line] 'goto-line-with-feedback)

The new function is quite useful that I would like to use it all the
time and replace the default `goto-line` with the new, enhanced
function. Which means I rebind the new function to the same key
sequence that originally was bound to `goto-line`.

Normally, I'd have something like this, where the key sequence is
hard-coded:

    (global-set-key (kbd "M-g g") 'goto-line-with-feedback)

However, using
[`remap`](https://www.gnu.org/software/emacs/manual/html_node/elisp/Remapping-Commands.html)
eliminates the hard-coding. It takes the key-binding assigned
currently to `goto-line` and re-binds it to the new function.

