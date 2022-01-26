+++
title = "openapi-yaml-mode"
author = [7696122]
lastmod = 2022-01-19T10:19:39+09:00
tags = ["swagger", "openapi", "emacs"]
draft = false
+++

## package {#package}

```elisp
(package! openapi-yaml-mode
  :recipe (:host github
            :repo "magoyette/openapi-yaml-mode"))
```


## config {#config}

```elisp
(use-package! openapi-yaml-mode
  :config
  (setq openapi-yaml-use-yaml-mode-syntax-highlight t))
```
