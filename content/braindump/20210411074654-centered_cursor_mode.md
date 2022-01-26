+++
title = "centered-cursor-mode"
author = [7696122]
lastmod = 2022-01-19T10:33:29+09:00
tags = ["cursor", "scroll", "emacs"]
draft = false
+++

## package {#package}

```elisp
(package! centered-cursor-mode)
```


## config {#config}

```elisp
(use-package! centered-cursor-mode
  :config
  ;; (setq ccm-vpos-init ;; '(round (* 21 (ccm-visible-text-lines)) 34)
  ;;       'center
  ;;       )
  (setq ccm-vpos-inverted -1)
  (setq ccm-recenter-at-end-of-file t)
  ;; (global-centered-cursor-mode -1)
  )
```
