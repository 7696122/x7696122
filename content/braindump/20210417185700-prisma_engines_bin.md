+++
title = "prisma-engines-bin"
author = [7696122]
lastmod = 2022-01-19T10:32:57+09:00
tags = ["nix", "prisma"]
draft = false
+++

## nix/prisma-engines-bin.nix {#nix-prisma-engines-bin-dot-nix}

<https://binaries.prisma.sh/all%5Fcommits/e421996c87d5f3c8f7eeadd502d4ad402c89464d/linux-nixos/introspection-engine.gz>  
<https://github.com/prisma/prisma-engines/archive/refs/tags/2.21.2.tar.gz>  
<https://github.com/prisma/docs/blob/main/content/200-concepts/050-overview/500-under-the-hood.mdx>  

<https://binaries.prisma.sh/all%5Fcommits/c1455d0b443d66b0d9db9bcb1bb9ee0d5bbc511d/debian-openssl-1.1.x/query-engine.gz.sha256>  
<https://binaries.prisma.sh/all%5Fcommits/c1455d0b443d66b0d9db9bcb1bb9ee0d5bbc511d/debian-openssl-1.1.x/introspection-engine.gz.sha256>  
<https://binaries.prisma.sh/all%5Fcommits/c1455d0b443d66b0d9db9bcb1bb9ee0d5bbc511d/debian-openssl-1.1.x/migration-engine.gz.sha256>  
<https://binaries.prisma.sh/all%5Fcommits/c1455d0b443d66b0d9db9bcb1bb9ee0d5bbc511d/debian-openssl-1.1.x/prisma-fmt.gz.sha256>  


## shell.nix {#shell-dot-nix}

```nix
with import <nixpkgs> { };

# prisma-bin.nix
let
  prismaDrv = { stdenv, fetchurl, openssl, zlib, autoPatchelfHook, lib }:
    let
      hostname = "binaries.prisma.sh";
      channel = "all_commits";
      target = "debian-openssl-1.1.x";
      baseUrl = "https://${hostname}/${channel}";
      commit = "e421996c87d5f3c8f7eeadd502d4ad402c89464d";
      hashes = {
        query-engine =
          "bf18419c1b2262b6b4742295eca64f8464af008ecdc25a613871491085ba9cf7";
        introspection-engine =
          "3e7fce182b6b7bc33cc7c696213988dcdf09a40dd3b9084c6c86a3071af7922c";
        migration-engine =
          "05ab4a735fa384702255a2198c802b6073f8675f56e57a54af0fcc004bc448e2";
        prisma-fmt =
          "053e053f0e984364f993c4b61b2495664004f128908db59e61d1b81461f6eab4";
      };
      files = lib.mapAttrs (name: sha256:
        fetchurl {
          url = "${baseUrl}/${commit}/${target}/${name}.gz";
          inherit sha256;
        }) hashes;
      unzipCommands =
        lib.mapAttrsToList (name: file: "gunzip -c ${file} > $out/bin/${name}")
        files;
    in stdenv.mkDerivation rec {
      pname = "prisma-bin";
      version = "2.21.2";
      nativeBuildInputs = [ autoPatchelfHook zlib openssl ];
      phases = [ "buildPhase" "postFixupHooks" ];
      buildPhase = ''
        mkdir -p $out/bin
        ${lib.concatStringsSep "\n" unzipCommands}
        chmod +x $out/bin/*
      '';
    };

  prismaPkg = callPackage prismaDrv { };

in mkShell {
  buildInputs = [ nodejs-14_x yarn prismaPkg ];
  shellHook = ''
    export PRISMA_MIGRATION_ENGINE_BINARY="${prismaPkg}/bin/migration-engine"
    export PRISMA_QUERY_ENGINE_BINARY="${prismaPkg}/bin/query-engine"
    export PRISMA_INTROSPECTION_ENGINE_BINARY="${prismaPkg}/bin/introspection-engine"
    export PRISMA_FMT_BINARY="${prismaPkg}/bin/prisma-fmt"
    export PATH="$PATH:$PWD/node_modules/.bin"
    export DEBUG="engine"
  '';
}
```
