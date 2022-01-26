+++
title = "elegant-agenda-mode"
author = [7696122]
lastmod = 2022-01-19T10:24:03+09:00
tags = ["agenda", "org"]
draft = false
+++

## package {#package}

```elisp
(package! elegant-agenda-mode)
```


## config {#config}

```elisp
(use-package! elegant-agenda-mode
  :hook (org-agenda-mode . elegant-agenda-mode)
  :custom
  (elegant-agenda-header-preference
   ;; 'thin
   'regular
   )
  (elegant-agenda-is-mono-font t)
  ;; :config
  ;; (remove-hook! org-agenda-mode-hook 'elegant-agenda-mode)
  ;; (setq org-agenda-custom-commands
  ;;       '(("d" "Today"
  ;;          ((tags-todo "SCHEDULED<\"<+1d>\"&PRIORITY=\"A\""
  ;;                      ((org-agenda-skip-function
  ;;                        '(org-agenda-skip-entry-if 'todo 'done))
  ;;                       (org-agenda-overriding-header "High-priority unfinished tasks:")))
  ;;           (agenda "" ((org-agenda-span 'day)
  ;;                       (org-scheduled-delay-days -14)
  ;;                       (org-agenda-overriding-header "Schedule")))
  ;;           (tags-todo "SCHEDULED<\"<+1d>\""
  ;;                      ((org-agenda-skip-function
  ;;                        '(or (org-agenda-skip-entry-if 'done)
  ;;                             (air-org-skip-subtree-if-priority ?A)))
  ;;                       (org-agenda-overriding-header "Tasks:")))))))
  )
```
