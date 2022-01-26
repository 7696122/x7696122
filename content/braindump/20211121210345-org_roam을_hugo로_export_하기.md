+++
title = "org-roam을 hugo로 export 하기"
author = [7696122]
lastmod = 2022-01-19T10:19:52+09:00
tags = ["org-roam", "org-mode", "evergreen-note"]
draft = false
+++

## org-roam-directory {#org-roam-directory}

```sh
echo $org_roam_directory
```


## ~org-roam-directory~/.dir-locals.el {#org-roam-directory-dot-dir-locals-dot-el}

<a id="code-snippet--.dir-locals.el"></a>
```elisp
((nil . ((org-hugo-base-dir . "~/7696122.github.io")
         (org-hugo-section . "braindump"))))
```

```elisp
(require 'ox-hugo)
<<root>>
```


## Tag Processing {#tag-processing}

```elisp
(defun org-hugo--tag-processing-fn-roam-tags(tag-list info)
  "Process org roam tags for org hugo"
  (if (org-roam-file-p)
    (append tag-list
      '("braindump")
      (mapcar #'downcase (org-roam--extract-tags)))
    (mapcar #'downcase tag-list)
    ))

(add-to-list 'org-hugo-tag-processing-functions 'org-hugo--tag-processing-fn-roam-tags)
```


## Backlinks Processing {#backlinks-processing}

```elisp
(defun org-hugo--org-roam-backlinks (backend)
  (when (equal backend 'hugo)
  (when (org-roam-file-p)
    (beginning-of-buffer)
    (replace-string "{" "")
    (beginning-of-buffer)
    (replace-string "}" "")
    (end-of-buffer)
    (org-roam-buffer--insert-backlinks))))
(add-hook 'org-export-before-processing-hook #'org-hugo--org-roam-backlinks)
```


## Ref {#ref}

[Exporting Org Roam notes to hugo - Sidharth Arya](https://sidhartharya.me/exporting-org-roam-notes-to-hugo/)  
[Evergreen note-writing as fundamental unit of knowledge work](https://notes.andymatuschak.org/z3SjnvsB5aR2ddsycyXofbYR7fCxo7RmKW2be)
