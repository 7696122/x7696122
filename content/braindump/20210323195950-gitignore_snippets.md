+++
title = "gitignore-snippets"
author = [7696122]
lastmod = 2022-01-19T10:36:40+09:00
tags = ["git", "gitignore", "emacs"]
draft = false
+++

## package {#package}

```elisp
(package! gitignore-snippets)
```


## config {#config}

```elisp
(use-package! gitignore-snippets
  :config
  ;; Make sure to call `gitignore-snippets-init' after yasnippet loaded.
  (gitignore-snippets-init))
```
