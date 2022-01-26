+++
title = "company-posframe"
author = [7696122]
lastmod = 2022-01-19T10:29:34+09:00
tags = ["company"]
draft = false
+++

## package {#package}

```elisp
(package! company-posframe)
```


## config {#config}

```elisp
(use-package! company-posframe
  :config
  (company-posframe-mode 1)
  (require 'desktop) ;this line is needed.
  (push '(company-posframe-mode . nil)
        desktop-minor-mode-table)
  )
```
