+++
title = "dashboard-project-status"
author = [7696122]
lastmod = 2022-01-19T10:26:03+09:00
tags = ["dashboard", "emacs"]
draft = false
+++

## package {#package}

```elisp
(package! dashboard-project-status)
```


## config {#config}

```elisp
(use-package! dashboard-project-status
  :after dashboard
  ;; :config
  ;; (add-to-list 'dashboard-item-generators
  ;;              `(project-status . ,(dashboard-project-status *your directory*)))
  ;; (add-to-list 'dashboard-items '(project-status) t)
  ;; (setq dashboard-items '((project-status . 10)
  ;;                         (recents        . 10)
  ;;                         (agenda         . 10)))
  )
```
