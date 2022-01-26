+++
title = "org-mode export and tangle"
author = [7696122]
lastmod = 2022-01-19T10:20:55+09:00
tags = ["function", "org-mode"]
draft = false
+++

[Exporting and Tangling simultaneously in org mode - Emacs Stack Exchange](https://emacs.stackexchange.com/questions/24645/exporting-and-tangling-simultaneously-in-org-mode)  

```elisp
(defun _export-tangle ()
  "Shortcut for exporting and tangling the current org-mode buffer."
  (interactive)
  (org-babel-tangle (org-org-export-to-org)))

(defun _export-tangle-file (filepath)
  "Shortcut for exporting and tangling the org-mode file at `filepath'."
  (org-babel-tangle (org-org-export-to-org (find-file (expand-file-name filepath))))
  (kill-buffer))
```
