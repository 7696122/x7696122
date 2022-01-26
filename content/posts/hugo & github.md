+++
title = "hugo, github"
author = [7696122]
date = 2021-07-29T00:00:00+09:00
lastmod = 2022-01-18T10:18:37+09:00
tags = ["채움"]
draft = true
+++

"남들이 보지 않을 것처럼 써라."를 실행하기 위해 수차례 시작하기만 하고 지속하지 못했던 블로그를 다시 만들었고 그 방법과 과정에 대한 기록을 남긴다.  

1.  android에서 [Termux](https://termux.com/)로 리눅스 환경을 만든다  
    1.  블루투스 키보드만 있으면 어디서든 블로그를 관리 할 수 있다
2.  hugo로 블로그를 만든다
3.  github에 2에서 만들어진 블로그를 올려둔다  
    1.  main branch
4.  github에 변경이 있을 때마다 빌드, 배포가 된다  
    1.  gh-pages branch에 빌드 결과가 배포된다


## termux {#termux}


## hugo {#hugo}

termux에 hugo 설치
: ```sh
    pkg install hugo
    ```


hugo 사이트 생성
: ```sh
    hugo new site myblog
    ```


git 초기화
: ```sh
    cd quickstart
    git init
    ```


테마 설정
: ```sh
    hugo mod init github.com/7696122/7696122.github.io
    ```
    
    ```toml
    theme = ["github.com/theNewDynamic/gohugo-theme-ananke", "github.com/crisrojas/Zettels"]
    ```


테스트 포스트 작성
: ```sh
    hugo new posts/my-first-post.md
    ```


## Github Pages {#github-pages}

github action
: <https://github.com/peaceiris/actions-gh-pages>  
    
    ```yaml
    name: github pages
    
    on:
      push:
        branches:
    ​      - main  # Set a branch to deploy
          pull_request:
    
            jobs:
              deploy:
                runs-on: ubuntu-20.04
                steps:
    ​              - uses: actions/checkout@v2
                    with:
                      submodules: true  # Fetch Hugo themes (true OR recursive)
                      fetch-depth: 0    # Fetch all history for .GitInfo and .Lastmod
    ​
                      - name: Setup Hugo
                        uses: peaceiris/actions-hugo@v2
                        with:
                          hugo-version: 'latest'
                          extended: true
    ​
                          - name: Build
                            run: hugo --minify
    ​
                            - name: Deploy
                              uses: peaceiris/actions-gh-pages@v3
                              if: github.ref == 'refs/heads/main'
                              with:
                                github_token: ${{ secrets.GITHUB_TOKEN }}
                                publish_dir: ./public
    
    ```


github pages 설정
: github action이 실행되고 나면 빌드 결과를 gh-pages 브랜치에 업데이트 되기에  
    github pages 설정에서 soure를 gh-pages 브랜치로 변경해야 한다.  
    
    <https://github.com/7696122/7696122.github.io/settings/pages>
