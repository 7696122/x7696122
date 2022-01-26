+++
title = "icons-in-terminal"
author = [7696122]
lastmod = 2022-01-19T10:22:23+09:00
draft = false
+++

## package {#package}

```elisp
(package! icons-in-terminal
  :recipe
  (:host github
   :repo "seagle0128/icons-in-terminal.el"))
```


## config {#config}

```elisp
;; (add-to-list 'load-path "~/.local/share/icons-in-terminal/")
;; (require 'icons-in-terminal)
;; (insert (icons-in-terminal 'oct_flame)) ; C-h f icons-in-terminal[RET] for more info

(use-package! icons-in-terminal)
```
