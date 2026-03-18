# nix-flakes-elm

Nix Flakes + Elm の開発環境サンプルです。

## 必要なもの

- [Nix](https://nixos.org/download/) (Flakes 有効化済み)
- [direnv](https://direnv.net/)

## セットアップ

### 1. `flake.nix` の用意

以下の内容で `flake.nix` を作成します。

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

### 2. `.envrc` の用意

以下の内容で `.envrc` を作成します。

```
use flake
```

direnv に許可を与えます。

```bash
direnv allow
```

これにより、ディレクトリに入ると自動で Nix の開発シェル（Node.js、corepack、elm）が有効になります。

### 3. 依存パッケージのインストール

```bash
pnpm install
```

## 開発サーバーの起動

ターミナルを2つ使います。

**ターミナル 1 — elm-watch（Elm のコンパイル & hot reload）**

```bash
pnpm dev
```

elm-watch が `src/Main.elm` を監視し、変更があると `build/main.js` を自動で再コンパイルします。

**ターミナル 2 — 静的ファイルサーバー**

```bash
pnpm serve
```

http://localhost:3000 をブラウザで開いてください。

## プロジェクト構成

```
.
├── flake.nix          # Nix 開発環境の定義
├── .envrc             # direnv 設定（use flake）
├── elm.json           # Elm プロジェクト設定
├── elm-watch.json     # elm-watch 設定
├── package.json       # npm パッケージ管理
├── index.html         # エントリーポイント
├── build/
│   └── main.js        # elm-watch が出力するコンパイル済み JS
└── src/
    └── Main.elm       # Elm ソースコード
```

## 設定ファイルの概要

**`elm-watch.json`**

```json
{
    "port": 8000,
    "targets": {
        "My target name": {
            "inputs": ["src/Main.elm"],
            "output": "build/main.js"
        }
    }
}
```

elm-watch の WebSocket サーバーはポート 8000 で動作します。ブラウザへの hot reload はこの WebSocket 経由で行われます。
