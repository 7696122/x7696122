+++
title = "workaround: projectile의 search를 vertico로"
author = [7696122]
lastmod = 2022-01-19T10:19:32+09:00
tags = ["emacs"]
draft = false
+++

## config {#config}

```elisp
;; $doom-emacs-dir/modules/config/default/+evil-bindings.el:310
(and (featurep! :completion vertico)
     (map! :leader
           :desc "Search project" "/" #'+vertico/project-search))
```
