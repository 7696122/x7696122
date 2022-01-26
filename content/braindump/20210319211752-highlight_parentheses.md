+++
title = "highlight-parentheses"
author = [7696122]
lastmod = 2022-01-19T10:38:36+09:00
tags = ["paren", "emacs"]
draft = false
+++

## package {#package}

```elisp
(package! highlight-parentheses)
```


## config {#config}

```elisp
(use-package! highlight-parentheses
  :custom
  (highlight-parentheses-colors `(nil))
  (highlight-parentheses-highlight-adjacent t)
  :custom-face
  (highlight-parentheses-highlight  ((t (:underline t :weight bold))))
  ;; :hook
  ;; (prog-mode . highlight-parentheses-mode)

  :config
  ;; (custom-set-faces '(highlight-parentheses-highlight
  ;;                     ((t (:underline t
  ;;                          :weight bold
  ;;                          :inverse-video nil
  ;;                          ))) t))

  (global-highlight-parentheses-mode +1))
```
