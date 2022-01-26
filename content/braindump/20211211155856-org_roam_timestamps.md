+++
title = "org-roam-timestamps"
author = [7696122]
lastmod = 2022-01-19T10:19:11+09:00
tags = ["org-roam", "roam", "emacs"]
draft = false
+++

org-roam :ctime: :mtime:  


## package {#package}

```elisp
(package! org-roam-timestamps)
```


## config {#config}

```elisp
(use-package! org-roam-timestamps
  :after org-roam
  ;; :custom
  ;; (org-roam-timestamps-timestamp-parent-file t)
  ;; (org-roam-timestamps-remember-timestamps t)
  :config
  (org-roam-timestamps-mode))
```
