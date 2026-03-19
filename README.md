# nix-flakes-elm

## Setup

0. set `flake.nix`, `.envrc` and `.gitignore`

- flake.nix

```nix
{
  description = "elm flake sample";
  inputs = {
    nixpkgs.url = "github:NixOS/nixpkgs/nixpkgs-unstable";
    flake-utils.url = "github:numtide/flake-utils";
  };

  outputs =
    inputs:
    inputs.flake-utils.lib.eachDefaultSystem (
      system:
      let
        pkgs = inputs.nixpkgs.legacyPackages.${system};
      in
      {
        devShells.default = pkgs.mkShell {
          packages = with pkgs; [
            # JavaScript / TypeScript
            nodejs
            corepack
            # Elm
            elmPackages.elm
          ];
        };
      }
    );
}
```

- .envrc

```
use flake
```

- .gitignore

```
# Elm
/build
/elm-stuff
/node_modules


# Nix
/.direnv
```

1. setup devShell

```sh
direnv allow
```

2. pnpm init

```sh
pnpm init
```

3. install `elm-watch` and `serve`

```sh
pnpm add -D elm-watch serve
```

4. elm-watch init

```sh
pnpm exec elm-watch init
```

5. elm init

```sh
elm init
```

6. update `package.json`

- package.json

```json
{
  ...
  (omitted)
  "scripts": {
    "dev": "serve .",
    "elm": "elm-watch hot"
  },
  (omitted)
  ...
}
```

7. create `index.html` and `src/Main.elm`

- index.html

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
  </head>
  <body>
    <script src="build/main.js"></script>
  </body>
</html>
```

- src/Main.elm

```elm
module Main exposing (main)

import Html exposing (text)


main =
    text "Hello, World!"
```

## Start Dev Server

1. start elm dev server (websocket)

```sh
pnpm run elm
```

2. start http dev server (http)

```sh
pnpm run dev
```

## References

https://lydell.github.io/elm-watch/
