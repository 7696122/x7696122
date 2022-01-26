+++
title = "tmux-inactive-panes"
author = [7696122]
lastmod = 2022-01-19T10:40:49+09:00
tags = ["dim", "tpm", "tmux"]
draft = false
+++

nvim과 의존성이 강한 듯  
제대로 동작 안함  

```conf
set -g @plugin 'jtbairdsr/tmux-inactive-panes'
```


## Ref {#ref}

[How does the tmux color palette work? - Super User](https://superuser.com/questions/285381/how-does-the-tmux-color-palette-work)  

<http://www.deanbodenham.com/learn/tmux-pane-colours.html>  
<https://tech.serhatteker.com/post/2019-06/tmux-focus/>  

```sh
for i in {0..255}; do
    printf "\x1b[38;5;${i}mcolour${i}\x1b[0m\n"
done
```

```sh
for i in {0..255}; do  printf "\x1b[38;5;${i}mcolor%-5i\x1b[0m" $i ; if ! (( ($i + 1 ) % 8 )); then echo ; fi ; done
```
