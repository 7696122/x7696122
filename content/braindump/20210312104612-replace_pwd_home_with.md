+++
title = "Replace pwd $HOME with `~`"
author = [7696122]
lastmod = 2022-01-19T10:42:03+09:00
tags = ["xsh"]
draft = false
+++

```sh
alias pwd='pwd | sed "s,$HOME,\~,"'
```
