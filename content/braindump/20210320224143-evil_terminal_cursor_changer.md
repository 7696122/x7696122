+++
title = "evil-terminal-cursor-changer"
author = [7696122]
lastmod = 2022-01-19T10:37:52+09:00
tags = ["cursor", "tty", "emacs"]
draft = false
+++

## package {#package}

```elisp
;; (package! evil-terminal-cursor-changer :disable t)
;; (unpin! evil-terminal-cursor-changer)
;; (package! evil-terminal-cursor-changer
;;   :recipe (
;;            :branch "master"
;;            ))
;; (package! evil-terminal-cursor-changer
;;     ;; HACK Fix #3845: original package is abandoned. This fork fixes a breaking
;;     ;;      bug that crashes the first terminal frame opened from the daemon.
;;     :recipe (:host github :repo "kisaragi-hiu/evil-terminal-cursor-changer")
;;     :pin "24755a18a311226933946f7218684dab5960ebc9")
```


## config {#config}

```elisp
(use-package! evil-terminal-cursor-changer
  :custom
  (etcc-use-color t))
```
