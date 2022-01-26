+++
title = "terminal-focus-reporting"
author = [7696122]
lastmod = 2022-01-19T10:27:21+09:00
draft = false
+++

## package {#package}

```elisp
(package! terminal-focus-reporting)
```


## config {#config}

```elisp
(use-package! terminal-focus-reporting
  :if (display-graphic-p)
  :config
  (terminal-focus-reporting-mode))
```
