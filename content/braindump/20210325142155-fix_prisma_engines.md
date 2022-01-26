+++
title = "fix prisma engines"
author = [7696122]
lastmod = 2022-01-19T10:36:09+09:00
tags = ["prisma"]
draft = false
+++

`pkg install protobuf`  

```sh
#! /usr/bin/env nix-shell
#! nix-shell -i bash -p pkg-config zlib openssl nodejs cargo

#yarn # install all packages, so the node_modules folder is available

git clone https://github.com/prisma/prisma-engines/
cd prisma-engines
source .envrc # idk if needed
cargo build --release
cd ..
```

```sh
FROM=prisma-engines/target/release
TO_CLI=node_modules/@prisma/cli
TO_ENGINES=node_modules/@prisma/engines
cp ${FROM}/introspection-engine ${TO_CLI}/introspection-engine-linux-nixos
cp ${FROM}/migration-engine     ${TO_CLI}/migration-engine-linux-nixos
cp ${FROM}/query-engine         ${TO_CLI}/query-engine-linux-nixos
cp ${FROM}/prisma-fmt           ${TO_CLI}/prisma-fmt-linux-nixos

cp ${FROM}/introspection-engine ${TO_ENGINES}/introspection-engine-linux-nixos
cp ${FROM}/migration-engine     ${TO_ENGINES}/migration-engine-linux-nixos
cp ${FROM}/query-engine         ${TO_ENGINES}/query-engine-linux-nixos
cp ${FROM}/prisma-fmt           ${TO_ENGINES}/prisma-fmt-linux-nixos
```


## Ref {#ref}

<https://github.com/prisma/prisma/issues/3026>
