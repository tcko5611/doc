[HOME](../README.md)

# Setting keys
1. (global-set-key (kbd "<f2>") 'set-mark-command) 
2. (global-set-key (kbd "<f1>") 'goto-line)

# load library
```
(setq load-path (cons (expand-file-name "~/emacs/lisp") load-path))
(require 'markdown-mode)
```
