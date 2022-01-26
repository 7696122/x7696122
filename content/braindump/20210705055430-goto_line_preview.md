+++
title = "goto-line-preview"
author = [7696122]
lastmod = 2022-01-19T10:27:11+09:00
tags = ["goto-line", "emacs"]
draft = false
+++

## package {#package}

```elisp
(package! goto-line-preview)
```


## config {#config}

```elisp
(use-package! goto-line-preview
  :config
  (global-set-key [remap goto-line] 'goto-line-preview))
```
