+++
title = "smooth-scrolling"
author = [7696122]
lastmod = 2022-01-19T10:33:22+09:00
tags = ["scroll", "emacs"]
draft = false
+++

## package {#package}

```elisp
(package! smooth-scrolling)
```


## config {#config}

```elisp
(use-package! smooth-scrolling
  :config
  (setq smooth-scroll-margin 5)
  (setq smooth-scroll-strict-margins t)
  (smooth-scrolling-mode 1))
```
