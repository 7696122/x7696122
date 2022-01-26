+++
title = "smart-cursor-color"
author = [7696122]
lastmod = 2022-01-19T10:35:11+09:00
tags = ["color", "cursor", "emacs"]
draft = false
+++

## package {#package}

```elisp
(package! smart-cursor-color)
```


## config {#config}

```elisp
(use-package! smart-cursor-color
  :when window-system
  :custom-face
  ;; (face-background 'default)
  (cursor ((t (:background "red"))))
  :init
  (global-hl-line-mode -1)
  (set-face-attribute 'cursor nil
                      :background (face-background 'default))
  :hook
  (after-init . (lambda ()
                  (global-hl-line-mode -1)
                  (smart-cursor-color-mode +1))))
```
