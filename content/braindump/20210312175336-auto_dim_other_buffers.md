+++
title = "auto-dim-other-buffers"
author = [7696122]
lastmod = 2022-01-19T10:41:02+09:00
tags = ["dim", "emacs"]
draft = false
+++

## package {#package}

```elisp
(package! auto-dim-other-buffers)
```


## config {#config}

```elisp
(use-package! auto-dim-other-buffers
  :custom-face
  (auto-dim-other-buffers-face
   ((t (:background
        ,(if (eq frame-background-mode 'light)
             "gainsboro"
           "color-234")
        ))))
  :custom
  (auto-dim-other-buffers-dim-on-switch-to-minibuffer nil)
  (auto-dim-other-buffers-dim-on-focus-out t)

  ;; :hook
  ;; (after-init .
  ;;             (lambda ()
  ;;               (when (fboundp 'auto-dim-other-buffers-mode)
  ;;                 (auto-dim-other-buffers-mode t))))
  :config
  (auto-dim-other-buffers-mode +1))
```
