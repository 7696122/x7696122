+++
title = "heroku에 create-react-app 배포하기"
author = [7696122]
lastmod = 2022-01-19T10:37:05+09:00
draft = false
+++

```sh
npx create-react-app $APP_NAME
cd $APP_NAME
heroku create $APP_NAME --buildpack mars/create-react-app
git push heroku main
heroku open
```


## Ref {#ref}

[mars/create-react-app-buildpack - Buildpacks - Heroku Elements](https://elements.heroku.com/buildpacks/mars/create-react-app-buildpack)
