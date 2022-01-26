+++
title = "org-cite-csl-activate"
author = [7696122]
lastmod = 2022-01-19T10:18:18+09:00
draft = false
+++

## package {#package}

```emacs-lisp
(package! oc-csl-activate
  :recipe
  (:host github
   :repo "andras-simonyi/org-cite-csl-activate"))
```


## config {#config}

```emacs-lisp
(use-package! oc-csl-activate
  :config
  (setq org-cite-activate-processor 'csl-activate)
  (add-hook 'org-mode-hook (lambda () (cursor-sensor-mode 1))))
```
