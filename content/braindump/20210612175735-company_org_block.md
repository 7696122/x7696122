+++
title = "company-org-block"
author = [7696122]
lastmod = 2022-01-19T10:29:40+09:00
tags = ["company", "emacs"]
draft = false
+++

## package {#package}

```elisp
(package! company-org-block)
```


## config {#config}

```elisp
(use-package! company-org-block
  :custom
  (company-org-block-edit-style 'auto) ;; 'auto, 'prompt, or 'inline
  :hook ((org-mode . (lambda ()
                       (setq-local company-backends '(company-org-block))
                       (company-mode +1)))))
```
