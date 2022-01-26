+++
title = "flycheck-swagger-cli"
author = [7696122]
lastmod = 2022-01-19T10:19:38+09:00
tags = ["swagger", "openapi", "emacs"]
draft = false
+++

[GitHub - magoyette/flycheck-swagger-cli: An Emacs Flycheck checker for OpenAP...](https://github.com/magoyette/flycheck-swagger-cli)  

```sh
npm install -g swagger-cli
```


## package {#package}

```elisp
(package! dash)
(package! flycheck-swagger-cli
  :recipe (:host github
            :repo "magoyette/flycheck-swagger-cli"))
```


## config {#config}

```elisp
(use-package! flycheck-swagger-cli)
```
