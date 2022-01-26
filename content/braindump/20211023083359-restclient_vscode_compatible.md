+++
title = "restclient-vscode-compatible"
author = [7696122]
lastmod = 2022-01-19T10:21:18+09:00
draft = false
+++

[Asya-kawai/restclient-vscode-compatible](https://www.yatex.org/gitbucket/Asya-kawai/restclient-vscode-compatible)  


## package {#package}

```elisp
(package! restclient
  :recipe
  (:host nil
   :repo "https://www.yatex.org/gitbucket/git/Asya-kawai/restclient-vscode-compatible.git"))
```


## config {#config}

```elisp
;; (load-file "~/.emacs.d/.local/straight/repos/restclient-vscode-compatible/restclient-vscode-compatible.el")
;; (use-package! restclient
;;   :load-path
;;   "~/.emacs.d/.local/straight/repos/restclient-vscode-compatible"
;;   ;; (expand-file-name "straight/repos/restclient-vscode-compatible" straight-base-dir)
;;   )
(add-to-list 'load-path' "~/.emacs.d/.local/straight/repos/restclient-vscode-compatible")
(require 'restclient)
```
