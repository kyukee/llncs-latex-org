# LNCS Springer LaTeX template for Org mode

Forked from https://github.com/schnorr/llncsorg

This template is used to write articles in Org mode, with Emacs, and export them as LaTeX and PDF documents.

To create the LaTeX and PDF documents, you can either:

- open the repository directory in a terminal and run 'make'
- open the llncs.org file inside Emacs and run 'M-x compile'. Use 'make' as the compile command. After that you can use 'M-x recompile'

If compiling from inside Emacs, here are some tips:

- run 'M-x split-window-right' to have two windows open
- install the pdf-tools package for viewing PDF's
  - Emacs can already show PDF's by default, but this package is a liitle bit nicer to use
- put (global-auto-revert-mode t) in your init file to automatically refresh PDF's
- you can also refresh PDF's manually with the 'g' key

Here's a function to automatically compile the Org document and switch to the PDF file if successful. You can put it in your init file.

```lisp
(defun my/latex-compile-and-open-pdf ()
  "Compile an org document into a Latex pdf and open it
   if the compilation is successful"
  (interactive)
  (let ((filename (file-name-sans-extension buffer-file-name))
        (compilation-exit-code (shell-command "make")))  ;; 'compile' is async, 'shell-command' is synchronous
    ;; check if the exit code is 0, which means there are no errors
    (when (= 0 compilation-exit-code)
      (find-file-other-window (expand-file-name (concat filename ".pdf"))))))

;; dont ask for confirmation for refreshing PDF buffers
(setq revert-without-query '(".pdf"))
```

It can be called with 'M-x my/latex-compile-and-open-pdf' or assigned to a keybind.

```lisp
;; assign function to 'Ctrl-c l'
(define-key org-mode-map (kbd "C-c l") 'my/latex-compile-and-open-pdf)
```

You can also make it run automatically when a file is saved.

```lisp
;; toggle compile automatically on save
(defun toggle-org-latex-export-on-save ()
  (interactive)
  (if (memq 'my/latex-compile-and-open-pdf after-save-hook)
      (progn
        (remove-hook 'after-save-hook 'my/latex-compile-and-open-pdf t)
        (message "Disabled org export on save for current buffer..."))
    (add-hook 'after-save-hook 'my/latex-compile-and-open-pdf nil t)
    (message "Enabled org export on save for current buffer...")))
```
