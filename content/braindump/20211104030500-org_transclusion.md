+++
title = "org-transclusion"
author = [7696122]
lastmod = 2022-01-19T10:20:36+09:00
tags = ["roam", "emacs"]
draft = false
+++

## package {#package}

```elisp
(package! org-transclusion
  ;; :recipe (:host github
  ;;          :repo "nobiot/org-transclusion"
  ;;          :branch "main"
  ;;          :files ("*.el"))
  )
```


## config {#config}

```elisp
(use-package! org-transclusion
  :defer
  :after org
  :init
  (map!
   :map global-map "<f12>" #'org-transclusion-add
   :leader
   :prefix "n"
   :desc "Org Transclusion Mode" "t" #'org-transclusion-mode))
```
