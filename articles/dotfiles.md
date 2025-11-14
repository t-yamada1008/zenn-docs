title: "GNU Stowを使ってdotfiles管理を楽にする"
emoji: "⚫️"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["開発環境"]
published: true
---

dotfilesのシンボリックリンク面倒だなと思ってたけど、GNU Stowというものがあるらしい。
とりあえず試してみる。

## GNU Stowとは

公式
<https://www.gnu.org/software/stow/>

> GNU Stow is a symlink farm manager

実態としてはシンボリックリンクのマネージャー。
シンボリックリンクを管理した結果、dotfilesの管理が楽になるというイメージ。

## インストール

Macだとhomebrewが使える。
WindowsでWSL2/Ubuntuに設定する場合は下記。

```sh
% sudo apt update # パッケージ更新
% sudo apt install -y stow # stowインストール
```

dotfiles本体はGitHubにて管理しているので、そちらから持ってくる。

## 実行

dotfiles配下にて実行する。

```sh
% pwd # dotfilesのディレクトリに移動
<fullpath>/dotfiles
% stow -v -t ~ zsh git fzf
```

既存の同名ファイルがある場合は失敗するので注意。

```sh
% stow -v -t ~ zsh git fzf
LINK: .fzf.zsh => workspace_tyamada/dotfiles/fzf/.fzf.zsh
WARNING! stowing git would cause conflicts:
  * existing target is neither a link nor a directory: .gitconfig
WARNING! stowing zsh would cause conflicts:
  * existing target is neither a link nor a directory: .zshrc
All operations aborted.
```

下記のオプションを使うことで自動移動し、シンボリックリンクも入れ替えてくれる。

```sh
% stow -v -t ~ --adopt zsh git fzf
MV: .zshrc -> workspace_tyamada/dotfiles/zsh/.zshrc
LINK: .zshrc => workspace_tyamada/dotfiles/zsh/.zshrc
MV: .gitconfig -> workspace_tyamada/dotfiles/git/.gitconfig
LINK: .gitconfig => workspace_tyamada/dotfiles/git/.gitconfig
LINK: .fzf.zsh => workspace_tyamada/dotfiles/fzf/.fzf.zsh
```

## 注意事項
.sshの取り扱いには注意
そもそも入れないという選択肢もある。主に管理したいのはzshrcなどだし...。

## 参考URL

<https://zenn.dev/apollyon/articles/b39ca3e0a94436>
<https://qiita.com/yug1224/items/8bbb701039c059e74b95>
<https://qiita.com/adwin/items/3e3c7fefe2d8f8430d4e>
