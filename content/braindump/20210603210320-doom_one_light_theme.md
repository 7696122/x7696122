+++
title = "doom-one-light-theme"
author = [7696122]
lastmod = 2022-01-19T10:30:38+09:00
tags = ["theme", "doom"]
draft = false
+++

## config {#config}

```elisp
(use-package! doom-one-light-theme
  :custom-face
  (custom-state ((t (:background "grey"
                     :foreground "black"
                     :distant-foreground "black"))))
  (custom-saved ((t (:background "grey"
                     :foreground "black"
                     :weight bold))))
  (custom-invalid ((t (:background "#e45649"
                       :foreground "black"))))
  (custom-modified ((t (:background "#4078f2"
                        :foreground "black"))))
  :custom
  (doom-one-light-brighter-comments t)
  (doom-one-light-brighter-modeline t)
  (doom-one-light-padded-modeline t))
```
