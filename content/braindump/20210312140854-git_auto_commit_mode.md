+++
title = "git-auto-commit-mode"
author = [7696122]
lastmod = 2022-01-19T10:41:45+09:00
tags = ["tool", "git", "emacs"]
draft = false
+++

## package {#package}

```elisp
(package! git-auto-commit-mode)
```


## config {#config}

```elisp
;; (custom-set-variables
;;  ;; custom-set-variables was added by Custom.
;;  ;; If you edit it by hand, you could mess it up, so be careful.
;;  ;; Your init file should contain only one such instance.
;;  ;; If there is more than one, they won't work right.
;;  '(safe-local-variable-values
;;    '((gac-ask-for-summary-p)
;;      (gac-ask-for-summary-p . t)
;;      (gac-silent-message-p . t)
;;      (gac-automatically-push-p . t)) nil nil "Customized with use-package files"))

(use-package! git-auto-commit-mode
  :custom
  (safe-local-variable-values
   '((gac-ask-for-summary-p)
     (gac-ask-for-summary-p . t)
     (gac-silent-message-p . t)
     (gac-automatically-push-p . t)))
  (gac-ask-for-summary-p nil)
  (gac-silent-message-p t)
  ;; (gac-automatically-push-p t)
  )
```
