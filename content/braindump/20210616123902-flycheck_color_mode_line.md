+++
title = "flycheck-color-mode-line"
author = [7696122]
lastmod = 2022-01-19T10:29:08+09:00
tags = ["modeline"]
draft = false
+++

## package {#package}

```elisp
(package! flycheck-color-mode-line)
```


## config {#config}

```elisp
(use-package! flycheck-color-mode-line
  :after flycheck
  :hook (flycheck-mode . flycheck-color-mode-line-mode))
```
