+++
title = "tmux reload config"
author = [7696122]
lastmod = 2022-01-19T10:43:00+09:00
tags = ["tmux"]
draft = false
+++

## << Reload config {#reload-config}

```conf
bind-key R source-file ~/.tmux.conf \; display-message "~/.tmux.conf reloaded"
bind-key M split-window -h "vim ~/.tmux.conf"
```
