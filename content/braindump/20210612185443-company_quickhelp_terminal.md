+++
title = "company-quickhelp-terminal"
author = [7696122]
lastmod = 2022-01-19T10:29:32+09:00
tags = ["company", "emacs"]
draft = false
+++

## package {#package}

```elisp
(package! company-quickhelp-terminal)
```


## config {#config}

```elisp
(use-package! company-quickhelp-terminal
  :config
  (with-eval-after-load 'company-quickhelp
    (company-quickhelp-terminal-mode +1)))
```
