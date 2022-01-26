+++
title = "termux cursor color"
author = [7696122]
lastmod = 2022-01-19T10:37:56+09:00
tags = ["color", "cursor", "termux"]
draft = false
+++

```elisp
;; cat ~/.termux/colors.properties
# green cursors
cursor:  #00bb00
```

-   shape

<!--listend-->

```sh
echo -e '\e[2 q' # Change to block
echo -e '\e[4 q' # Change to underline
echo -e '\e[6 q' # Change to bar
```

-   color

<!--listend-->

```elisp
(shell-command (format "echo -ne %s" "\033]12;#ff0000\007"))
;; echo -ne "\033]12;#ff0000\007"    # red
```

-   blink

<!--listend-->

```sh
# printf '\e[2 q'
# printf '\e[4 q'
# printf '\e[6 q'
```


## Ref {#ref}

-   [Reddit - termux - How do I change cursor?](https://amp.reddit.com/r/termux/comments/d9rxeo/how%5Fdo%5Fi%5Fchange%5Fcursor/)
-   [termux/termux-app#757 Cursor style](https://github.com/termux/termux-app/issues/757)
-   [termux/termux-app#153 Terminal emulation: blinking cursor?](https://github.com/termux/termux-app/issues/153)
