+++
title = "company-yasnippet"
author = [7696122]
lastmod = 2022-01-19T10:27:26+09:00
tags = ["yasnippet", "company", "emacs"]
draft = false
+++

## config {#config}

```elisp
(use-package! company-yasnippet
  :config
  (add-hook 'js-mode-hook
            (lambda ()
              (set (make-local-variable 'company-backends)
                   '((company-dabbrev-code company-yasnippet)))))

  (push '(company-semantic :with company-yasnippet) company-backends)
  (global-set-key
   (kbd "C-c y") ;; (kbd "C-x C-s")
   'company-yasnippet))
```
