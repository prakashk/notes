Title: Copy, comment and paste current line or region  
Date: Fri Apr  5 10:43:48 2013  
Tags: Emacs

When I am editing code, often I copy current line, comment it and paste it below before beginning to edit the cloned code. So,  after doing a lot of times this I wrote up this little function. I have used emacs for a long time, but my elisp-fu is still next to nothing, so it took me a while to get things right. Especially since I wanted it to work for either active region or current line.

    (defun copy-comment-paste ()
      "copy active region/current line, comment, and then paste"
      (interactive)
      (unless (use-region-p)
        (progn
          (beginning-of-line 2)
          (push-mark (line-beginning-position 0))))
      (kill-ring-save (region-beginning) (region-end))
      (comment-region (region-beginning) (region-end))
      (yank)
      (exchange-point-and-mark)
      (indent-according-to-mode))

Here's the gist for the same: https://gist.github.com/prakashk/5319782

[Cory Koch](https://plus.google.com/100350223839708376940) [suggested `C-c c`](https://plus.google.com/104527465464156441280/posts/S66B9FmyQ4K) as the key to bind this function.

[Noah Friedman](https://plus.google.com/105068366727758843481) came up with this improved version:

    (defun nf-copy-and-comment-lines (beg end)
      "Insert a copy of the lines in region and comment them.
    When transient-mark-mode is enabled, if no region is active then only the
    current line is acted upon.
     
    If the region begins or ends in the middle of a line, that entire line is
    copied, even if the region is narrowed to the middle of a line.
    The copied lines are commented according to mode.
     
    Current position is preserved."
      (interactive "r")
      (save-excursion
        (save-restriction
          (widen)
          (when (and transient-mark-mode (not (use-region-p)))
            (setq beg (line-beginning-position)
                  end (line-beginning-position 2)))
     
          (goto-char beg)
          (setq beg (line-beginning-position))
          (goto-char end)
          (unless (= (point) (line-beginning-position))
            (setq end (line-beginning-position 2)))
     
          (goto-char beg)
          (insert-before-markers (buffer-substring-no-properties beg end))
          (comment-region beg end))
     
        ;; Don't modify region or display if called as a function.
        (when (called-interactively-p 'all)
          ;; If narrowed, make sure newly commented lines are visible in the
          ;; narrowed region.
          (when (> (point-min) beg)
            (narrow-to-region beg (point-max)))
     
          ;; Make sure newly commented lines are visible in the window if at the
          ;; very top.
          (unless (pos-visible-in-window-p beg)
            (set-window-start (selected-window) beg)))))
