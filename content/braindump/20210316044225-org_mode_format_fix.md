+++
title = "org-mode format fix"
author = [7696122]
lastmod = 2022-01-19T10:40:14+09:00
tags = ["workaround", "org-mode", "emacs"]
draft = false
+++

org 파일에 스타일을 사용할 수 있는데 (~, =, \_ 등으로 감싸)  
한글에서 쓸 땐 간격이 없는 경우 스타일이 제대로 적용되질 않아  
아래 방법으로 해결하려 했으나 다른 문제가 발생  
[emacs - How to make part of a word bold in org-mode - Stack Overflow](https://stackoverflow.com/questions/1218238/how-to-make-part-of-a-word-bold-in-org-mode)  


## noweb {#noweb}

```elisp
(after! org
  ;; (setq org-babel-default-header-args
  ;;       (cons '(:noweb . "yes")
  ;;             (assq-delete-all :noweb org-babel-default-header-args)))

  ;; (progn
  ;;   ;; https://www.reddit.com/r/orgmode/comments/nau7kp/emphasize_a_word_partially/
  ;;   (setcar org-emphasis-regexp-components " \t('\"{[:alpha:]")
  ;;   (setcar (nthcdr 1 org-emphasis-regexp-components) "[:alpha:]- \t.,:!?;'\")}\\")
  ;;   (org-set-emph-re 'org-emphasis-regexp-components org-emphasis-regexp-components)
  ;;   )

  ;; (progn
  ;;   ;; https://newbedev.com/how-to-make-part-of-a-word-bold-in-org-mode
  ;;   (setcar org-emphasis-regexp-components " \t('\"{[:alpha:]")
  ;;   (setcar (nthcdr 1 org-emphasis-regexp-components) "[:alpha:]- \t.,:!?;'\")}\\")
  ;;   (org-set-emph-re 'org-emphasis-regexp-components org-emphasis-regexp-components))

  ;; (with-eval-after-load 'org
  ;;   ;; https://emacs.stackexchange.com/questions/60533/org-mode-overline-emphasis-not-able-to-be-followed-by-other-characters
  ;;   ;; (setf (nth 1 org-emphasis-regexp-components) "-_^[:space:].,:!?;'\")}\\[")
  ;;   ;; (setf (nth 4 org-emphasis-alist) '("~" (:overline t) verbatim))

  ;;   ;; chars for prematch
  ;;   (setcar org-emphasis-regexp-components            "     ('\"{“”\[\\")
  ;;   ;; chars for postmatch
  ;;   (setcar (nthcdr 1 org-emphasis-regexp-components) "\] -   .,!?;''“”\")}/\\“”")
  ;;   ;; forbidden chars
  ;;   (setcar (nthcdr 2 org-emphasis-regexp-components) "    \t\r\n,\"")
  ;;   ;; body
  ;;   (setcar (nthcdr 3 org-emphasis-regexp-components) ".")
  ;;   ;; max newlines
  ;;   (setcar (nthcdr 4 org-emphasis-regexp-components) 1)
  ;;   (org-set-emph-re 'org-emphasis-regexp-components org-emphasis-regexp-components))
  )
```
