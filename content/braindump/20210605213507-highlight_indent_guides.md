+++
title = "highlight-indent-guides"
author = [7696122]
lastmod = 2022-01-19T10:30:32+09:00
tags = ["emacs"]
draft = false
+++

## config {#config}

```elisp
(use-package! highlight-indent-guides
  ;; :if (featurep! :ui indent-guides)
  ;; :after (contrast-color)
  :custom-face
  (highlight-indent-guides-even-face ((t (:inherit fill-column-indicator))))
  (highlight-indent-guides-odd-face ((t (:inherit fill-column-indicator))))
  (highlight-indent-guides-stack-even-face ((t (:inherit fill-column-indicator))))
  (highlight-indent-guides-stack-odd-face ((t (:inherit fill-column-indicator))))
  (highlight-indent-guides-top-even-face ((t (:inherit fill-column-indicator))))
  (highlight-indent-guides-top-odd-face ((t (:inherit fill-column-indicator))))
  (highlight-indent-guides-stack-character-face ((t (:inherit fill-column-indicator))))
  (highlight-indent-guides-character-face ((t (:inherit fill-column-indicator))))
  :custom
  (highlight-indent-guides-suppress-auto-error t)
  (highlight-indent-guides-responsive 'stack)
  (highlight-indent-guides-delay 0))
```
