+++
title = "web-mode-edit-element"
author = [7696122]
lastmod = 2022-01-19T10:26:42+09:00
tags = ["web-mode", "emacs"]
draft = false
+++

## package {#package}

```elisp
(package! web-mode-edit-element)
```


## config {#config}

```elisp
(use-package! web-mode-edit-element
  :hook
  (web-mode . web-mode-edit-element-minor-mode))
```
