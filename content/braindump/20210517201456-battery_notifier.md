+++
title = "battery-notifier"
author = [7696122]
lastmod = 2022-01-19T10:31:17+09:00
draft = false
+++

## package {#package}

```elisp
(package! battery-notifier)
```


## config {#config}

```elisp
(use-package! battery-notifier
  :custom
  (battery-notifier-capacity-low-threshold (* 15 2))
  (battery-notifier-capacity-critical-threshold (* 5 2))
  :config
  (battery-notifier-mode +1))
```
