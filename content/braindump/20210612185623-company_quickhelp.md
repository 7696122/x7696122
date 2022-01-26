+++
title = "company-quickhelp"
author = [7696122]
lastmod = 2022-01-19T10:29:30+09:00
tags = ["company", "emacs"]
draft = false
+++

## package {#package}

```elisp
(package! company-quickhelp)
```


## config {#config}

```elisp
(use-package! company-quickhelp
  :config
  (setq company-quickhelp-delay 0.1)
  ;; (setq company-quickhelp-use-propertized-text (display-graphic-p))
  (setq company-quickhelp-use-propertized-text t)
  (company-quickhelp-mode +1))
```
