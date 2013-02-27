Title: Emacs: are we inside a comment?  
Date: Fri Jan 25 16:49:29 2013  
Tags: emacs, elisp

Today I learned how to find out if the `point` in an emacs buffer is inside a comment block. There is a function `syntax-ppss` which returns "Parse-Partial-Sexp State at POS, defaulting to point."

It returns a list of elements describing the state of parsing the syntax at `point`. The 4th element in this list is `t` (true) if `point` is inside a comment, or `nil` otherwise.

Wrapping this in a nice little function:

    (defun inside-comment-p ()
      "return t if point is inside a comment block"
      (nth 4 (syntax-ppss)))

Perhaps there is a built-in predicate that returns the same in a more straight-forward manner, but this is as close as we can get to that.
