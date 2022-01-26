+++
title = "tmux split same directory"
author = [7696122]
lastmod = 2022-01-19T10:41:16+09:00
tags = ["tmux"]
draft = false
+++

[Tmux open new pane in same directory · GitHub](https://gist.github.com/william8th/faf23d311fc842be698a1d80737d9631)  

```conf
# Set new panes to open in current directory
bind c new-window -c "#{pane_current_path}"
bind '"' split-window -c "#{pane_current_path}"
bind % split-window -h -c "#{pane_current_path}"
```
