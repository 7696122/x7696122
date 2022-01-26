+++
title = "Insecure world writable dir …, mode 040777"
author = [7696122]
lastmod = 2022-01-19T10:19:01+09:00
draft = false
+++

[ruby on rails - warning: Insecure world writable dir /usr/local/bin in PATH, ...](https://stackoverflow.com/questions/3952243/warning-insecure-world-writable-dir-usr-local-bin-in-path-mode-040777)  

```sh
chmod go-w /Applications/Emacs.app
chmod go-w /Applications/Emacs.app/Contents
chmod go-w /Applications/Emacs.app/Contents/MacOS
chmod go-w /Applications/Emacs.app/Contents/MacOS/bin
```
