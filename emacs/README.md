[HOME](../README.md)

# Setting keys
1. (global-set-key (kbd "<f2>") 'set-mark-command) 
2. (global-set-key (kbd "M-g") 'goto-line)

# load library
```
(setq load-path (cons (expand-file-name "~/emacs/lisp") load-path))
(require 'markdown-mode)
```

# Move in file
* C-M-a
* C-M-e
	move in a function
* C-M-p
* C-M-n
	move in a parenthesis

# Edit Commands

## Numeric Arguments
* **Ctrl-u** n, repeat n times for the next command

# Gdb
* use **set startup-with-shell off** to avoid invoke subprocess with shell
