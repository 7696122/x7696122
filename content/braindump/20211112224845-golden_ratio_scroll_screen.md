+++
title = "golden-ratio-scroll-screen"
author = [7696122]
lastmod = 2022-01-19T10:20:08+09:00
draft = false
+++

## package {#package}

```elisp
(package! golden-ratio-scroll-screen)
```


## config {#config}

```elisp
(use-package! golden-ratio-scroll-screen
  :config
  (global-set-key [remap scroll-down-command] 'golden-ratio-scroll-screen-down)
  (global-set-key [remap scroll-up-command] 'golden-ratio-scroll-screen-up))
```
