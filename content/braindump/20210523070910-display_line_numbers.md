+++
title = "display-line-numbers"
author = [7696122]
lastmod = 2022-01-19T10:31:12+09:00
tags = ["line-number", "emacs"]
draft = false
+++

## config {#config}

```elisp
(use-package! display-line-numbers
  :custom
  (display-line-numbers-width 3)
  :custom-face
  (line-number-current-line ((t (:inherit line-number :underline t)))))
```
