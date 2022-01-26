+++
title = "automatically include subfolder of org files?"
author = [7696122]
lastmod = 2022-01-19T10:18:09+09:00
draft = false
+++

```sh
for f in $(find ~/org/roam -name "*.org"); do
    echo "#+INCLUDE: " $f :only-contents t
done
```


## References {#references}

[org mode - automatically include subfolder of org files? - Emacs Stack Exchange](https://emacs.stackexchange.com/questions/32243/automatically-include-subfolder-of-org-files)
