Title: This is strictly a test  
Date: Wed Mar  6 17:18:06 2013  
Tags: elisp

My first reasonably-sized elisp function I wrote from scratch.

It prompts for title and tags and pre-populates a new buffer with the
entered information, so new notes can easily be created. The file name
for the note is derived from the title, with consecutive non-word
characters (`\W`) replaced by a `-` (dash) and the current date
prepended. If a note with the constructed file name already exists,
the file is simply visited.

    (defvar pk-notes-root-directory
      (expand-file-name "~/play/notes/site/_posts")
      "Root directory containing all posts")
     
    (defun add-new-note ()
      "create a new note"
      (interactive)
      (let* ((note-title (read-from-minibuffer "Title: "))
             (note-timestamp (current-time-string))
             (note-tags (read-from-minibuffer "Tags: "))
             (note-file-name (concat (format-time-string "%Y-%m-%d")
                                     "-"
                                     (replace-regexp-in-string "[^A-Za-z0-9_]+" "-" note-title)
                                     ".md"))
             (note-file-path (concat pk-notes-root-directory "/" note-file-name)))
        (if (file-exists-p note-file-path)
            (progn
              (message "Note %s already exists" note-file-name)
              (find-file note-file-path)
              (forward-paragraph))
          (progn
            (find-file note-file-path)
            (insert "Title: " note-title "  \n"
                    "Date: " note-timestamp "  \n"
                    "Tags: " note-tags "\n\n")))))

Pretty useful, if I say so myself.
