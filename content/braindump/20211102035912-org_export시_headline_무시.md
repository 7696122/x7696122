+++
title = "org export시 headline 무시"
author = [7696122]
lastmod = 2022-01-19T10:20:39+09:00
tags = ["export", "org"]
draft = false
+++

```elisp
(require 'ox-extra)
(ox-extras-activate '(ignore-headlines))
```
